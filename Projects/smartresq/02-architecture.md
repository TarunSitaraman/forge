# SmartResQ Architecture

## Mental Model

SmartResQ is a **real-time alert pipeline with fallback routing**. Key insight: dispatch decision must complete in <5 seconds with fallback notification if primary channels timeout or fail.

```
Patient Input (SOS or wearable anomaly)
  ↓
Ingestion (validate, deduplicate, encrypt)
  ↓
Scoring (compare vitals to patient baseline)
  ↓
Rule Evaluation (detect anomalies, suppress spam)
  ↓
Dispatch Decision (nearest hospital + ambulance)
  ↓
Parallel Notification (hospital + ambulance, not serial)
  ↓
Fallback (SMS/WhatsApp if APIs timeout/fail)
  ↓
Workflow Tracking (ambulance arrival, hospital handover)
  ↓
Audit Chain (tamper-resistant log)
```

**Why parallel, not serial?** Serial would add 10–20s latency (wait for hospital response, then dispatch ambulance). Parallel + fallback allows perceived latency to be ambulance ETA only.

**Why fallback?** Hospital EHR APIs can timeout (8s cutoff). Fallback SMS to admin ensures hospital gets notified even if EHR is down.

## Core Pipeline Services

| Service | Role |
|---------|------|
| **Ingestor** (Node, :3000) | HTTP entry point. Validates vitals schema, deduplicates (Redis 5-min TTL), encrypts AES-256-GCM, publishes to `vitals-raw` channel. Returns 202 + traceId. |
| **Aggregator** (Python subprocess) | Consumes `vitals-raw`, scores against patient baseline (Postgres query), emits to `vitals-normalized`. Exponential smoothing + z-score. Runs in same container as ingestor. |
| **Detection** (Python subprocess) | Consumes `vitals-normalized`, applies alert rules (thresholds, trends, suppression). Emits to `alerts` Redis channel. Runs in same container as ingestor. |
| **Dispatcher** (Node, worker) | Consumes from `alerts` channel (or Kafka fallback). Queues dispatch work to BullMQ. Retry logic (exponential backoff: 1s, 2s, 4s). Metrics on :9104. |
| **Orchestrator** (Node, :3012) | Dispatch decision engine. Routes to nearest hospital (bed capacity check) + nearest ambulance. Fault detection (8s hospital timeout → SMS fallback). State machine (PENDING → AMBULANCE_ASSIGNED → ARRIVED → HANDOVER_COMPLETED). |

## Integration Layer

| Service | Role |
|---------|------|
| **Hospital Integration** (:3005) | Intake queue, EHR FHIR push, bed management, SSE live feed, dispatch handover tracking. |
| **Ambulance Integration** (:3006) | CAD dispatch payload, GPS tracking, crew vitals, handover workflow, paramedic watch app. |
| **Patient Service** (:3003) | Registration, device pairing, SOS trigger, subscription management. |
| **PHR Core** (:3013) | FHIR document store. Receives emergency bundles from orchestrator. |
| **Relevance Engine** (:3014) | Context fetching on dispatch (<100ms): known conditions, allergies, medications, hospital preferences. |
| **Wearable Adapter** (:3009) | Normalizes Fitbit/Apple/Garmin/Polar/Omron vitals into common schema. |

## Cross-Cutting Services

| Service | Role |
|---------|------|
| **Staff Auth** (:3015) | JWT issuance + verification. Roles: doctor, nurse, admin, dispatcher. Cached token validation (1h TTL). |
| **ATNA Logger** (:3007) | Audit log service. Fire-and-forget pub/sub. Hash-chain tamper-resistant. Verification endpoint walks chain. |
| **Compliance Service** (:3008) | DPDP consent verification, SaMD risk analysis, regulatory checks. Blocks dispatch if consent missing. |
| **Billing Service** (:3010) | Insurance pre-authorization, subscription validation, plan checks. |
| **Insurer Integration** (:3011) | Insurer API adapter (pre-auth requests, data licensing). |

## Data Flow: SOS to Dispatch

1. **Patient app sends SOS**
   ```
   POST /alerts/sos
   { patientId, lat, lng }
   ```
   Ingestor validates JWT, checks rate-limit (15/hour), creates alert_id, enqueues BullMQ job. Returns 202 Accepted + traceId.

2. **Dispatcher consumes alert, calls orchestrator**
   ```
   POST /dispatch/decide
   { alertId, patientId, lat, lng, severity }
   ```
   Orchestrator queries hospitals (WHERE active_beds > 0 ORDER BY distance), queries ambulances (WHERE available AND distance < 5km). Scores both. Returns dispatch_id + routing.

3. **Orchestrator fires TWO parallel notifications (Promise.all)**
   
   **Hospital notification:**
   ```
   POST /hospital/{id}/incoming-patient
   ```
   Creates dispatch record, inserts into incoming_queue, fires SSE to hospital UI, posts pre-arrival FHIR bundle to EHR. Timeout 8s → fallback SMS to hospital admin.
   
   **Ambulance notification:**
   ```
   POST /ambulance/{id}/dispatch
   ```
   Sends CAD command (GPS, patient info, ETA). Timeout 5s → fallback WhatsApp to paramedic.

4. **Both services fire ATNA audit events**
   patient_id, dispatch_id, timestamp, outcome → audit_log table (hash-chain verified).

5. **Paramedic accepts dispatch via app**
   Ambulance integration records acceptance time (dispatch_latency_ms). Hospital UI shows "ambulance en route" status + live ETA.

6. **Handover**
   Paramedic marks handover complete. Hospital integration records completion. Dispatcher writes final dispatch record (total_latency_ms, status='completed').

## Key Design Decisions

| Decision | Why | Tradeoff |
|----------|-----|----------|
| **Pub/sub + BullMQ combo** | Real-time fan-out (aggregator, detection) + durable job persistence (dispatcher retry) | More complex than single queue. Mitigated by traceId correlation. |
| **Postgres + Redis Streams** | Audit trail + query-ability (Postgres) + exactly-once consumer groups (Streams). Streams flagged off for stability. | Two write paths. Advantage: zero in-flight loss on dispatcher crash. |
| **Parallel notification** | Reduces perceived latency. Hospital prep starts while ambulance en route. | If either fails, dispatch still recorded (no rollback). Fallback ensures eventual delivery. |
| **Fallback notification** | If hospital EHR down, SMS instead of failing dispatch. If ambulance CAD down, WhatsApp. | Duplicative notifications (patient may see SMS + app). Dedup on receive. |
| **7-day vitals window** | Trend detection (is HR rising over hours?) without full history in memory. | Memory tradeoff: ~1MB per patient. Cleanup job prunes old vitals. |
| **Postgres baselines** | Fast indexed lookups. Baseline = patient's average at time-of-day, recomputed weekly. | Async computation. First SOS uses global fallback. After 7 days, personalized. |

See [Reliability](./03-reliability.md) for how each design choice is validated.

## Request Flow Diagram

```
wearable-adapter → ingestor:3000/vitals
                        ↓
                   Redis vitals-raw channel
                        ↓
aggregator (subprocess) → baselines (Postgres) → vitals-normalized channel
                                                           ↓
detection (subprocess) → alert rules → alerts channel
                                            ↓
dispatcher (worker) ← BullMQ enqueue ← ingestor:3000/alerts/sos
                        ↓
            orchestrator:3012/dispatch/decide
                    ↙                    ↘
    hospital-integration:3005         ambulance-integration:3006
         ↓                                  ↓
    EHR (FHIR) + SMS fallback      CAD (EMRI) + WhatsApp fallback
         ↓                                  ↓
    incoming_queue (hospital UI)   ambulance app + watch
         ↓                                  ↓
    staff accepts → admits → handover complete
         ↓                                  ↓
    ATNA audit events (tamper-chained) ←---
```

## Summary

SmartResQ routes emergency alerts through 5 pipeline services (ingestor → aggregator → detection → dispatcher → orchestrator) with parallel fallback notification to 5 integration services (hospital, ambulance, patient, PHR, relevance). All decisions logged to tamper-resistant audit trail. <5s SLA enforced by circuit breakers, graceful degradation, and synthetic monitoring.

See [Components](./08-components.md) for individual service deep-dive.

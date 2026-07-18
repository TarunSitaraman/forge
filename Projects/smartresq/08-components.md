# SmartResQ Components Deep-Dive

## Ingestor Service (:3000)

**Entry point for all patient events. Node.js + Express.**

**Routes:**

| Route | Role | Auth |
|-------|------|------|
| `POST /vitals` | Wearable uploads normalized vitals (HR, SpO2, BP, temp). Validates, deduplicates, encrypts, publishes to `vitals-raw`. | JWT |
| `POST /alerts/sos` | Patient app triggers SOS. Derives location from GPS. Rate-limited (15/hour). Creates alert_id. Enqueues dispatch job. | JWT |
| `POST /alerts/sos/cancel` | Patient cancels SOS (if still pending). Idempotent. | JWT |
| `GET /trace/{traceId}` | Query observability: follow a vital through pipeline. Shows each service's processing time. | JWT |
| `POST /webhooks/{service}` | Callbacks from hospital/ambulance (arrival, handover). | INTERNAL_SERVICE_SECRET |
| `POST /comms/{dispatch_id}` | Post-dispatch communications (paramedic ↔ hospital staff). | JWT |
| `POST /alerts` | Direct alert injection (testing, external detection). | JWT or INTERNAL_SERVICE_SECRET |

**Vitals validation:** `src/services/vitals.js` checks patient exists, subscription active, DPDP consent, rate-limit. Early return if any check fails (all-or-nothing).

**Deduplication:** Redis key = `vitals:{patient_id}:{hash(vital_data)}` with 5-min TTL. Same vital within 5 min → 202 Accepted, no re-publish. Prevents storm of identical vitals (common if wearable syncs offline then bulk-uploads).

**Encryption:** AES-256-GCM. Key from `CRYPTO_KEY` env var. Nonce rotated per vital. Encrypted payload stored in Postgres (BYTEA). Decryption on read (by authorized services: hospital, relevance-engine, patient).

## Dispatcher (Worker Process)

**Stateless job consumer. Node.js.**

**Consumption modes:**

1. **Redis pub/sub (default, legacy).** Subscribes to `alerts` channel. Fire-and-forget. If dispatcher crashes, in-flight alerts lost.
2. **Redis Streams (new, flagged off).** Consumer group + PEL. On crash, pending messages reclaimed. Exactly-once semantics.

**Processing loop:**
```javascript
while (true) {
  const alert = await consumeAlert(); // pub/sub or streams
  if (!alert.alertId) { logger.warn('drop'); continue; }
  
  await enqueueDispatch(alert); // BullMQ job
}
```

**BullMQ job processing:**
```javascript
queue.process(async (job) => {
  const dispatch = await orchestrator.decide(job.data);
  // Parallel notification
  await Promise.all([
    hospital.notify(dispatch),
    ambulance.notify(dispatch),
  ]);
  return { status: 'dispatched' };
});
```

**Retry:** 3 retries with exponential backoff (1s, 2s, 4s). If all fail, job moves to failed queue (operator intervention needed).

**Metrics:** Counter `alerts_processed_total`, histogram `dispatch_decision_latency_ms`, gauge `queue_depth`.

**Metrics port:** :9104.

## Aggregator (Python Subprocess)

**Runs in same container as ingestor.**

**Consumes:** `vitals-raw` Redis channel.

**Processing:**
```python
1. Validate schema (has HR, timestamp, etc.)
2. Query Postgres baselines by patient_id + hour_of_day
3. Compute z-score: (vital - baseline.mean) / baseline.stddev
4. Store in vitals table (score column)
5. Emit to vitals-normalized channel
```

**Baseline strategy:**
- Global fallback: HR 60–100, SpO2 95–100, temp 36.5–37.5.
- Patient-specific: recomputed weekly from vitals table. Requires ≥100 samples. Falls back to global if insufficient history.
- Stored in `baselines` table (patient_id, hour_of_day, mean, stddev, sample_count).

**Stateless:** Aggregator itself is ephemeral. Baselines and vitals persist in Postgres. If aggregator crashes, vitals re-consumed from Redis Streams (if enabled) or lost (pub/sub).

## Detection Engine (Python Subprocess)

**Runs in same container as ingestor.**

**Consumes:** `vitals-normalized` Redis channel.

**Alert rules (hardcoded, should be externalized):**
```python
rules = [
  {
    'name': 'cardiac_alert',
    'condition': 'HR > 120 OR (HR > 100 AND SpO2 < 92)',
    'suppression_if': 'last_alert_5_min',
    'severity': 'CRITICAL',
  },
  {
    'name': 'tachycardia',
    'condition': 'HR > 100 AND z_score > 2',
    'severity': 'HIGH',
  },
]
```

**Alert suppression:** If alert issued < 5 min ago for same patient + rule, skip (prevents alert storm).

**Emits to `alerts` channel:**
```json
{
  "alertId": "alert-20260717-123456",
  "patientId": "patient-001",
  "severity": "CRITICAL",
  "suspectedCondition": "cardiac_alert",
  "vitals": { "HR": 125, "SpO2": 91 },
  "timestamp": "2026-07-17T12:34:56Z"
}
```

## Orchestrator Service (:3012)

**Dispatch decision engine. Node.js + Express. Stateless.**

**Endpoint: `POST /dispatch/decide`**

**Input:**
```json
{
  "alertId": "alert-123",
  "patientId": "patient-001",
  "lat": 13.0067,
  "lng": 80.2206,
  "severity": "CRITICAL"
}
```

**Logic:**
```javascript
1. Query hospitals (WHERE active_beds > 0 ORDER BY distance ASC)
2. Query ambulances (WHERE available AND distance(lat, lng) < 5km ORDER BY distance ASC)
3. Score each combo: ambulance_eta + hospital_eta + hospital_capacity + ambulance_reliability
4. Pick top combo; if score < threshold, fall back to next-best
5. Create dispatch record (state = PENDING)
6. Return routing decision
```

**Fault detection:** If hospital doesn't acknowledge within 8s, fire fallback SMS. If ambulance doesn't accept within 30s, escalate to next ambulance.

**State machine:**
```
PENDING (0 min)
  ↓ [ambulance accepts]
AMBULANCE_ASSIGNED (5 min)
  ↓ [ambulance at scene]
AMBULANCE_ARRIVED (15 min)
  ↓ [hospital receives patient]
HOSPITAL_RECEIVED (20 min)
  ↓ [paramedic handover complete]
HANDOVER_COMPLETED (30 min)
  ↓ [archive]
CLOSED
```

Timeout per state; exceeding timeout triggers escalation (re-route ambulance, activate backup hospital).

## Hospital Integration Service (:3005)

**Hospital-side workflow engine. Node.js + Express.**

**Routes:**

| Route | Role |
|-------|------|
| `POST /hospital/{id}/incoming-patient` | Inbound dispatch. Creates `incoming_queue` record. Fires SSE to hospital UI. Posts FHIR bundle to EHR. |
| `POST /hospital/{id}/acknowledge` | Staff acknowledges receipt. Records timestamp. |
| `POST /hospital/{id}/admit` | Patient checked into bed. Updates dispatch state. |
| `POST /hospital/{id}/handover` | Paramedic hands off. Patient = hospital's responsibility. Fires ATNA audit event. |
| `GET /hospital/{id}/incoming` | Hospital UI polls live queue. SSE-backed (real-time). |
| `POST /hospital/{id}/bed-status` | Manage bed availability (admin). Updates `active_beds`. |

**SSE stream:** Hospital UI connects to `/hospital/{id}/incoming` with SSE. Server maintains per-hospital Redis subscriber. When dispatch arrives, publishes to hospital's SSE channel. UI receives real-time update (no polling needed).

**EHR integration:** Posts to hospital's FHIR endpoint (configured per hospital in DB). Sends emergency bundle (patient info + vitals + dispatch metadata) before ambulance arrives. Fallback if EHR timeout (8s cutoff): SMS to hospital admin.

## Ambulance Integration Service (:3006)

**Paramedic-side workflow engine. Node.js + Express.**

**Routes:**

| Route | Role |
|-------|------|
| `POST /ambulance/{id}/dispatch` | Inbound dispatch. Includes patient info, GPS target, hospital address. Fires to ambulance app + watch. |
| `POST /ambulance/{id}/accept` | Paramedic accepts. Records timestamp. Updates dispatch state. |
| `POST /ambulance/{id}/location` | GPS check-in. Every 30s. Updates ETA to hospital. |
| `POST /ambulance/{id}/crew-vitals` | Paramedic's own vitals (HR, SpO2, stress). Stored for wellness + capacity assessment. |
| `POST /ambulance/{id}/arrived` | At scene. Updates dispatch state. |
| `POST /ambulance/{id}/handover` | At hospital, handing off patient. Triggers hospital acceptance. |
| `GET /ambulance/{id}/pending` | List active dispatches for this ambulance. |

**CAD integration:** Calls EMRI API if available (mock in dev, real in prod). Timeout 5s → fallback WhatsApp direct to paramedic.

**Watch app backfill:** Forwards dispatch to watch-ui (compact: patient name, vitals, ETA). Separate WebSocket or SSE connection.

## Cross-Cutting Services

### Staff Auth (:3015)
JWT issuance + verification. Roles: doctor, nurse, admin, dispatcher. Cached token validation (1h TTL). Refresh token rotation.

### ATNA Logger (:3007)
Audit log service. Fire-and-forget pub/sub from all services. Hash-chain tamper-resistant (SHA-256). Verification endpoint walks chain, reports first broken link.

### Compliance Service (:3008)
DPDP consent verification, SaMD risk analysis, regulatory checks. Blocks dispatch if consent missing. Checked before every dispatch.

### Billing Service (:3010)
Insurance pre-authorization, subscription validation, plan checks. Validates patient subscription active before ingesting vitals or dispatching.

### Insurer Integration (:3011)
Insurer API adapter. Pre-auth requests, data licensing. Bridges SmartResQ and insurer systems.

### Patient Service (:3003)
Registration, device pairing, SOS trigger, subscription management. Patient-facing operations.

### PHR Core (:3013)
FHIR document store. Receives emergency bundles from orchestrator. Persists as FHIR Bundles in Postgres.

### Relevance Engine (:3014)
Context fetching on dispatch (<100ms SLA): known conditions, allergies, medications, hospital preferences. Queries cached baseline data. Decrypts vitals on-demand.

### Wearable Adapter (:3009)
Normalizes Fitbit/Apple/Garmin/Polar/Omron vitals into common schema before ingestor. Device-specific OAuth token refresh.

## Service Startup Dependencies

```
Postgres + Redis (infrastructure layer)
  ↓
Ingestor (depends on Postgres + Redis)
  ↓
Aggregator + Detection (subprocesses, depend on Redis)
  ↓
Dispatcher (depends on Redis + Postgres + BullMQ)
  ↓
Orchestrator + Integration services (depend on Postgres + Redis)
  ↓
All others (parallel startup, inter-service calls tolerate timeouts)
```

See [Architecture](./02-architecture.md) for full system diagram.

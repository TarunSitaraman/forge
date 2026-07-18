# SmartResQ — Project Handoff & Architecture Notes

*Emergency response coordination platform. Real-time dispatch of ambulances and hospital pre-notification triggered by patient SOS or wearable health events. Production-grade, cloud-portable, India-focused, open for real hospital deployment.*

---

## Executive Summary

SmartResQ is a mature microservice system (Node.js + Python + React stack) that routes emergency dispatches with sub-second coordination across five actors: patient, ambulance, hospital, operations, and orchestration services. The engineering foundation is hardened (automated security gates, tamper-evident audit, proven disaster recovery, 60%+ test coverage). Current blockers for hospital go-live are infrastructure-related (single-node DigitalOcean droplet, no HA), not code.

**Ownership transition task:** understand the architecture, decision rationale, deployment model, reliability posture, and path to scale to multi-node cloud infrastructure (AWS/Azure IaC ready).

---

## 1. Architecture & Request Flow

### 1.1 System Mental Model

SmartResQ is a real-time **alert pipeline with fallback routing**. A patient trigger (SOS or vitals anomaly) → ingestion → scoring → dispatch decision → simultaneous notification to ambulance and hospital → workflow tracking → audit chain.

The critical insight: **the dispatch decision must complete in <5 seconds, with fallback notification if primary channels are slow or offline.**

```
Patient Input
  ├─ SOS from app (direct)
  └─ Wearable vitals (via ingestor)
       │
       ▼
  Alert Pipeline
       ├─ Ingestor validation + deduplication (Redis)
       ├─ Aggregator scoring against baselines
       └─ Detection engine rule evaluation
            │
            ▼
  Dispatch Decision
       ├─ Query: nearest hospital with capacity
       ├─ Query: nearest ambulance + ETA calc
       └─ Fallback: SMS/WhatsApp if APIs down
            │
            ▼
  Dual Notification (parallel, not serial)
       ├─ Ambulance (CAD system or WhatsApp)
       ├─ Hospital (EHR + intake queue)
       ├─ Family (phone/SMS/app)
       └─ Audit event (tamper-chained)
            │
            ▼
  Workflow Tracking
       ├─ Ambulance arrival confirmation
       ├─ Hospital handover completion
       └─ Status updates + communications
```

### 1.2 Core Pipeline Services

| Service | Port | Technology | Role |
|---------|------|-----------|------|
| **ingestor** | 3000 | Node.js/Express | HTTP ingestion point. Validates vitals schema, deduplicates (Redis 5-min TTL), encrypts AES-256-GCM at rest, publishes to `vitals-raw` Redis channel. Returns 202 + traceId for observability. |
| **aggregator** | — | Python (standalone) | Consumes vitals from `vitals-raw`, scores against patient baseline (stored in Postgres), emits to `vitals-normalized`. Implements exponential smoothing + z-score detection. Runs as a subprocess in the ingestor container. |
| **detection** | — | Python (standalone) | Consumes from `vitals-normalized`, applies alert rules (thresholds, trends, suppression). Emits to `alerts` Redis channel. Implements 7-day rolling history. Runs as a subprocess in the ingestor container. |
| **dispatcher** | 9104 | Node.js (worker) | Consumes from `alerts` Redis channel (or Kafka fallback). Queues dispatch work via BullMQ. Implements retry with exponential backoff. Exposes metrics on :9104. |
| **orchestrator** | 3012 | Node.js/Express | Dispatch coordination service. Routes to nearest hospital (with bed capacity check) + nearest ambulance. Implements fault detection (silent-drop fallback if hospital doesn't acknowledge within 8s). Manages dispatch state machine. |

### 1.3 Integration Layer

| Service | Port | Role |
|---------|------|------|
| **hospital-integration** | 3005 | Intake queue, EHR FHIR push, bed management, SSE live feed to hospital UI, dispatch handover tracking. |
| **ambulance-integration** | 3006 | CAD dispatch payload, GPS tracking, crew vitals ingestion, handover workflow, paramedic watch app backfill. |
| **patient-service** | 3003 | Registration, device pairing, profile, SOS trigger, subscription management. |
| **phr-core** | 3013 | FHIR document store. Receives emergency packets from orchestrator, persists as bundles. |
| **relevance-engine** | 3014 | Context fetching on dispatch: known conditions, allergies, medications, hospital preferences (queried in <100ms). |
| **wearable-adapter** | 3009 | Normalizes Fitbit/Apple/Garmin/Polar/Omron vitals into common schema before ingestor. |

### 1.4 Cross-Cutting Services

| Service | Port | Role |
|---------|------|------|
| **staff-auth** | 3015 | JWT issuance + verification. Roles: `doctor`, `nurse`, `admin`, `dispatcher`. Cached token validation (1-hour TTL). |
| **atna-logger** | 3007 | ATNA-compliant audit log. Fire-and-forget pub/sub from all services; hash-chain verified on read. Tamper-resistant via BEFORE INSERT trigger + signature field. |
| **compliance-service** | 3008 | DPDP consent verification, SaMD risk analysis, regulatory checks. Checks `dpdp_consent_given` before dispatch. |
| **billing-service** | 3010 | Insurance pre-authorization, subscription management, plan validation. |
| **insurer-integration** | 3011 | Insurer API adapter (pre-auth requests, data licensing). |

### 1.5 Data Flow Walkthrough: Patient SOS → Dispatch

```
1. Patient app POST /alerts/sos
   → ingestor validates JWT, derives patient_id, records alert_id
   → checks rate-limit (15 SOS per hour, fail-closed)
   → enqueues to BullMQ "dispatch" queue
   → returns 202 Accepted + traceId (for observability)

2. Dispatcher worker consumes queue, calls orchestrator POST /dispatch/decide
   → orchestrator queries hospitals (WHERE active_beds > 0 AND specialty matches)
   → queries ambulances (WHERE available AND distance < 5km)
   → selects best hospital (capacity + distance) + best ambulance
   → returns dispatch_id + routing decision

3. Orchestrator fires TWO parallel notifications (not serial):
   a) hospital-integration POST /hospital/{id}/incoming-patient
      → creates dispatch record, inserts into `incoming_queue`
      → fires SSE to hospital UI in real-time
      → POST to EHR FHIR endpoint (pre-arrival bundle)
      → timeout: 8s, fallback: SMS to hospital admin
   
   b) ambulance-integration POST /ambulance/{id}/dispatch
      → sends CAD command (GPS coordinates, patient info, ETA)
      → timeout: 5s, fallback: WhatsApp to paramedic
      → posts to ambulance app via SSE

4. Both services fire ATNA audit events (patient_id, dispatch_id, timestamp, outcome)
   → stored in audit_log table with hash-chain

5. Ambulance app shows incoming dispatch + patient preview
   → paramedic accepts via app
   → ambulance-integration records acceptance time (dispatch_latency_ms)
   → hospital UI shows "ambulance en route" status + live ETA

6. On arrival, paramedic marks handover complete
   → hospital-integration records completion + outcome
   → dispatcher writes final dispatch record (total_latency_ms, status='completed')
```

### 1.6 Key Design Decisions

| Decision | Rationale | Tradeoff |
|----------|-----------|----------|
| **Redis pub/sub + BullMQ queue combo** | Pub/sub for real-time fan-out (aggregator, detection); BullMQ for durable job persistence (dispatcher retry). | More complex than single queue; requires dual-write idempotence. Mitigated by traceId correlation. |
| **Dual-write to Postgres + Redis Streams** | Postgres for audit trail + query-ability; Streams for exactly-once consumer groups (consumer group + PEL reclaim). Streams mode toggled via `STREAMS_ENABLED` flag (off by default for stability). | Two write paths adds operational complexity. Advantage: Streams enabled = zero in-flight message loss on dispatcher crash. |
| **Simultaneous hospital + ambulance notification** | Reduces perceived latency; hospital prep starts while ambulance en route. | If either fails, dispatch is still recorded (rollback not needed). Fallback notification ensures eventual delivery. |
| **Fallback notification over primary** | If hospital EHR is down, SMS admin instead of failing dispatch. If ambulance CAD is down, send WhatsApp. | Duplicative notifications (patient may receive SMS + app); handled by dedup on receive. |
| **7-day vitals window in detection engine** | Allows trend detection (is HR rising over hours?) without loading full history into detection subprocess. | Memory tradeoff: 7 days * 1 reading/min * 100 bytes ≈ 1MB per patient. Mitigated by cleanup job. |
| **Patient baselines stored in Postgres** | Enables fast (indexed) lookups. Baseline = patient's personal average at time-of-day, recomputed weekly. | Requires migration job to populate. Runs async, non-blocking on first SOS. |

---

## 2. Strengths of the Current Design

### 2.1 Reliability

- **Idempotent dispatch.** Alert ID is a unique key; re-processing the same alert is safe (upsert, not insert). Traceability via traceId in all logs.
- **Circuit breakers (opossum)** on all external calls (EHR, CAD, insurance APIs). Fails fast, not cascading.
- **Exactly-once alert processing** via Redis Streams consumer groups + PEL (Pending Entry List) reclaim on dispatcher restart. Avoids duplicate dispatches.
- **Graceful shutdown** on all services. In-flight requests drain before process exits. BullMQ jobs re-queued if worker crashes mid-job.
- **Race condition prevention** via `FOR UPDATE SKIP LOCKED` in bed allocator and ambulance assignment queries.
- **Automatic rollback on deploy failure** — health check probes gateway; if any service unhealthy, automatic git revert + restart (captured in GitHub Actions).

### 2.2 Security

- **Secrets never in code or `.env` files.** Loaded at container startup via environment or (future) AWS Secrets Manager / Azure KeyVault via abstraction layer.
- **PHI encryption at rest** — vitals and patient records encrypted with AES-256-GCM. Key rotated via deployment.
- **RBAC enforcement** — every HTTP endpoint checks role (`requirePermission` middleware). Hospital staff can only view their own hospital's dispatches. Patients can only view their own records.
- **Audit trail tamper-resistant** — hash-chain (SHA-256) of audit events. Pre-existing rows re-chained by migration. Verification endpoint walks chain, reports first broken link.
- **Rate limiting** (Redis-backed, fail-closed). SOS endpoint: 15/hour. Vitals endpoint: 1000/hour. Blocking, not sampling.
- **Automated security gates** on every commit: gitleaks, semgrep (OWASP + Node rules), npm audit (critical), Trivy (fs + IaC). No secret scanning bypasses.
- **No SQL injection** — parameterized queries only. ESLint rule + semgrep rule forbid string interpolation in DB calls. CI enforces.
- **Auth tokens short-lived** (1 hour) with refresh token rotation.

### 2.3 Observability

- **Correlation IDs** (traceId) on every request. Logged at ingestor, aggregator, dispatcher, orchestrator. Allows tail-tracing a single SOS through the entire pipeline.
- **Prometheus metrics** on all services. Histogram: dispatch latency (p50, p99). Counter: alerts received, dispatches issued, failures. Gauge: queue depth. Scraped by Prometheus (:9090).
- **Grafana dashboards** for real-time dispatch volume, latency, hospital + ambulance response rates, error rates.
- **Synthetic monitor** — injects a probe vital every 5 minutes, verifies it reaches dispatch. Alerts on latency > 5s or missing steps.
- **Deep health checks** — `/health` endpoint checks Postgres connectivity, Redis connectivity, BullMQ queue health. Used by Docker health check and Kubernetes readiness probes.
- **Structured logging** (pino/Winston) — JSON output, tagged with service, level, traceId. All unhandled errors logged before response.

### 2.4 Cloud Portability

- **12-factor compliance verified** — no hardcoded hosts, no file paths, no sensitive data in code.
- **IaC ready** — `infra/` directory has Terraform modules for AWS (ap-south-1) + Azure (centralindia). Shared topology + provider overlays. Validated in CI via `terraform validate`.
- **Data residency enforced** — CI job `check-india-residency.sh` ensures no services run outside India (AMI regions, subnets, etc.).
- **Backup/restore tested** — restore drill passed 2026-06-14: RTO ~3s (pg_dump 1.2s + restore 1.7s), row counts matched, audit chain verified.

---

## 3. Weaknesses & Known Limitations

### 3.1 Deployment

**Single-node DigitalOcean droplet.** Architectural achilles heel for real hospital deployment. Running 12 services + Postgres + Redis + monitoring stack on one 1 vCPU/2 GB machine. **Cannot handle load.** Remediation: apply `infra/aws/` or `infra/azure/` Terraform to spin up managed Postgres, Redis, multi-AZ containerization (ECS/AKS).

### 3.2 Testing

**~60% code coverage.** Gate ratcheted from 47% to 60%. Path to 75%: supertest for route layer (200–400 lines), race-condition integration tests, hospital-side full-flow test. Not blocking deployment, but reduces confidence in production behavior.

**Integration tests use seeded data.** No realistic production-scale load testing (`k6` load script exists but not at scale). Should run load test with 1000+ concurrent patients before accepting real PHI.

**No pen-test.** Code audit is automated (semgrep, CodeQL, dependency scanning). Real pen-test commissioned by risk team before handling real medical data (high liability).

### 3.3 Data Model

**Vitals TTL logic is implicit.** Aggregator keeps 7-day rolling window in memory. Detection engine relies on cleanup job to prune old vitals from Postgres. If cleanup job fails silently, queries slow down. Remediation: add explicit TTL triggers in Postgres, emit metrics on vitals table size.

**Baseline calculation is async.** Patient baseline (personal average heart rate at time-of-day) computed weekly, stored in `baselines` table. First SOS uses global fallback baseline (HR 60–100 bpm). After a week, patient's baseline is personalized. Not an issue for ongoing operation, but requires patient to exist in system 7 days before anomaly detection is calibrated.

### 3.4 Compliance

**STREAMS_ENABLED flag is off by default.** Redis Streams provides exactly-once guarantees via consumer groups + PEL reclaim. Pub/sub alone can drop in-flight messages if dispatcher crashes. Production deployment should enable streams after verifying aggregator/detection consume from streams (Python side not yet verified). Currently toggled off for stability; next owner should verify end-to-end.

**Staging tier doesn't exist.** Prod deploy goes direct. Go-live checklist documents design of staging → smoke test → manual gate → prod; infrastructure doesn't exist yet. Should be added before hospital onboarding.

**Deploys run as root.** Not ideal. Create non-root `deploy` user on DigitalOcean droplet (docker group), re-key SSH, update deploy workflow.

---

## 4. Components Deep Dive

### 4.1 Ingestor (Node.js, :3000)

**Entry point for all patient events.** Serves five route groups:

| Route | Role | Auth |
|-------|------|------|
| `POST /vitals` | Wearable adapter uploads normalized vitals. Validates schema (HR, SpO2, BP, temp). Deduplicates (Redis 5-min TTL). Encrypts. Publishes to `vitals-raw`. | JWT |
| `POST /alerts/sos` | Patient app triggers SOS. Derives location from GPS. Rate-limited. Creates alert_id. Enqueues dispatch job. | JWT |
| `POST /alerts/sos/cancel` | Patient cancels SOS (if still pending). Idempotent. | JWT |
| `GET /trace/{traceId}` | Query observability: follow a vital or alert through the pipeline. Shows each service's processing time. | JWT |
| `POST /webhooks/{service}` | Callback hooks from hospital/ambulance integrations (arrival notification, handover completion). | INTERNAL_SERVICE_SECRET |
| `POST /comms/{dispatch_id}` | Post-dispatch communications (paramedic → hospital staff messages). | JWT |
| `POST /alerts` | Direct alert injection (for testing, or external anomaly detection). | JWT or INTERNAL_SERVICE_SECRET |

**Key file: `src/services/vitals.js`** — core validation logic. Checks patient exists, subscription active, DPDP consent given, rate-limit. Returns early if any check fails (no side effects until all pass).

**Deduplication:** Redis key = `vitals:{patient_id}:{vitals_hash}` with 5-min TTL. Same vitals within 5 minutes → 202 Accepted, no re-publish. Prevents storm of identical vitals (common if wearable syncs offline, then uploads bulk).

**Encryption:** `crypto` module AES-256-GCM. Key stored in `CRYPTO_KEY` env var. Nonce rotated per vital. Encrypted payload stored in Postgres `vitals.encrypted_payload` (BYTEA), decryption happens on read (hospital-integration, relevance-engine).

### 4.2 Dispatcher (Node.js worker)

**Stateless job consumer.** Runs as `npm run dispatcher` (separate process from ingestor).

**Two consumption modes:**
1. **Redis pub/sub (legacy, default).** Subscribes to `alerts` channel. Fire-and-forget. If dispatcher crashes, in-flight alerts are lost.
2. **Redis Streams (new, flagged off).** Consumer group + PEL. On crash, pending messages are reclaimed. Exactly-once semantics.

**Processing loop:**
```javascript
while (true) {
  const alert = await consumeAlert(); // pub/sub or streams
  if (!alert.alertId) { log('drop'); continue; }
  
  await enqueueDispatch(alert); // BullMQ
}
```

**BullMQ job:**
```javascript
job.process(async (alert) => {
  const dispatch = await orchestrator.decide(alert);
  // both services notified in parallel (Promise.all)
  await Promise.all([
    hospital.notify(dispatch),
    ambulance.notify(dispatch),
  ]);
  return { status: 'dispatched' };
});
```

**Retry logic:** BullMQ default is 3 retries with exponential backoff (1s, 2s, 4s). If all retry, job moves to `failed` queue (operator intervention needed). No automatic recovery; manually trigger via admin UI or `redis-cli`.

**Metrics:** Counter `alerts_processed_total`, histogram `dispatch_decision_latency_ms`, gauge `queue_depth`.

### 4.3 Aggregator (Python, subprocess)

**Standalone process launched by ingestor.** Listens to `vitals-raw` Redis channel.

**For each vital:**
```python
1. Validate schema (has HR, timestamp, etc.)
2. Query Postgres: SELECT * FROM baselines WHERE patient_id = ? AND time_of_day ~ hour
3. Compute z-score: (vital - baseline.mean) / baseline.stddev
4. Store in vitals table (score column)
5. Emit to vitals-normalized channel: {vital, z_score, time_processed}
```

**Baseline strategy:**
- Global fallback: HR 60–100, SpO2 95–100, temp 36.5–37.5.
- Patient-specific: recomputed weekly from vitals table. If not enough history (< 100 samples), fall back to global.
- Stores in `baselines` table (patient_id, hour, mean, stddev, sample_count). Updates on schedule, not on-demand (to avoid thrashing).

**No persistence:** aggregator itself is stateless. Baselines and vitals are in Postgres. If aggregator crashes, vitals are re-consumed from Redis stream (if streams enabled) or lost (if pub/sub).

### 4.4 Detection Engine (Python, subprocess)

**Subscribes to `vitals-normalized` channel.** Applies alert rules.

**Rule structure (hardcoded, next owner should externalize):**
```python
rules = [
  {
    'name': 'cardiac_alert',
    'condition': 'HR > 120 OR (HR > 100 AND SpO2 < 92)',
    'suppression_if': 'last_alert_5_min',  # avoid alert storm
    'severity': 'CRITICAL',
  },
  {
    'name': 'tachycardia',
    'condition': 'HR > 100 AND z_score > 2',
    'severity': 'HIGH',
  },
]
```

**Alert suppression:** if an alert was issued < 5 min ago for the same patient + same rule, skip (prevents spam). Configurable via rule.

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

**Operator observability:** detection engine logs rule evaluation (matched/suppressed). Trace via traceId.

### 4.5 Orchestrator (Node.js, :3012)

**Dispatch coordination service.** Stateless REST API.

**Endpoint: `POST /dispatch/decide`**
```javascript
POST /dispatch/decide
{
  alertId: "alert-123",
  patientId: "patient-001",
  lat: 13.0067,
  lng: 80.2206,
  severity: "CRITICAL"
}

Response:
{
  dispatchId: "dispatch-123",
  hospital: { id: "apollo-chennai", eta_minutes: 12 },
  ambulance: { id: "ambu-001", eta_minutes: 5 }
}
```

**Logic:**
```javascript
1. Query hospitals (SELECT * FROM hospitals WHERE active_beds > 0 ORDER BY distance(lat, lng))
2. Query ambulances (SELECT * FROM ambulances WHERE available AND distance < 5km)
3. Score each combo: ambulance_eta + hospital_eta + hospital_capacity + ambulance_reliability
4. Pick top combo; if score < threshold, fall back to next-best
5. Create dispatch record in DB
6. Return routing decision
```

**Fault detection:** if hospital doesn't acknowledge within 8s, fire fallback SMS. If ambulance doesn't accept within 30s, escalate to next ambulance.

**State machine:**
```
PENDING -> AMBULANCE_ASSIGNED -> AMBULANCE_ARRIVED -> HOSPITAL_RECEIVED -> HANDOVER_COMPLETED -> CLOSED
      (0 min)           (5 min)            (15 min)        (20 min)              (30 min)
```

Timeout per state; exceeding timeout triggers escalation (re-route ambulance, or activate backup hospital).

### 4.6 Hospital Integration (Node.js, :3005)

**Hospital-side workflow engine.**

**Routes:**
| Route | Role |
|-------|------|
| `POST /hospital/{id}/incoming-patient` | Inbound dispatch notification. Creates `incoming_queue` record. Fires SSE to hospital UI. Posts to EHR FHIR endpoint. |
| `POST /hospital/{id}/acknowledge` | Hospital staff acknowledges receipt of patient. Records timestamp. |
| `POST /hospital/{id}/admit` | Patient checked into bed. Updates dispatch state. |
| `POST /hospital/{id}/handover` | Paramedic hands off; patient now hospital's responsibility. Fires ATNA audit event. |
| `GET /hospital/{id}/incoming` | Hospital UI polls live incoming queue. SSE-backed; returns real-time updates. |
| `POST /hospital/{id}/bed-status` | Manage bed availability (admin). Updates `active_beds` count. |

**SSE stream:** hospital UI connects to `/hospital/{id}/incoming` with SSE. Server maintains a per-hospital Redis subscriber. When a dispatch comes in, server publishes to hospital's SSE channel. UI receives real-time update (no polling).

**EHR integration:** POST to hospital's FHIR endpoint (configured per hospital in DB). Sends emergency bundle (patient info + vitals + dispatch metadata) before ambulance arrives. Fallback if EHR down: SMS to hospital admin phone.

### 4.7 Ambulance Integration (Node.js, :3006)

**Paramedic-side workflow engine.**

**Routes:**
| Route | Role |
|-------|------|
| `POST /ambulance/{id}/dispatch` | Inbound dispatch. Includes patient info, GPS target, hospital address. Fires to ambulance app + watch app. |
| `POST /ambulance/{id}/accept` | Paramedic accepts dispatch. Records timestamp. Updates dispatch state. |
| `POST /ambulance/{id}/location` | GPS checkin. Ambulance sends location every 30s. Updates ETA to hospital. |
| `POST /ambulance/{id}/crew-vitals` | Paramedic's own vitals (HR, SpO2, stress level). Stored for wellness tracking + used by orchestrator to assess crew capacity. |
| `POST /ambulance/{id}/arrived` | At scene. Updates dispatch state. |
| `POST /ambulance/{id}/handover` | At hospital, handing off patient. Triggers hospital acceptance. |
| `GET /ambulance/{id}/pending` | List active dispatches for this ambulance. |

**CAD integration:** if EMRI (ambulance system) is available, dispatch route calls EMRI API (mock in dev, real in prod). If CAD down (timeout > 5s), fallback to WhatsApp direct to paramedic.

**Watch app backfill:** ambulance integration forwards dispatch to watch-ui (compact display: patient name, vitals, ETA). Separate WebSocket or SSE connection.

---

## 5. Database Schema & Key Tables

### 5.1 Core Tables

| Table | Role |
|-------|------|
| `patients` | Patient registry. PK: `patient_id` (UUID). Includes ABHA ID (Indian universal health ID), subscription plan, DPDP consent flag. |
| `patient_devices` | Wearable enrollments. FK: `patient_id`. Stores device brand, model, OAuth token (encrypted), `last_sync` timestamp. |
| `hospitals` | Hospital registry. PK: `hospital_id`. Coordinates, specialty array, bed capacity, EHR endpoint. |
| `ambulances` | Ambulance registry. PK: `ambulance_id`. Crew info, base station, vehicle status. |
| `dispatches` | Dispatch audit trail. PK: `dispatch_id`. Records alert_id, ambulance_id, hospital_id, latencies, fallback methods used, completion status. **THIS IS THE KEY TABLE** for SLA tracking. |
| `vitals` | Raw vital signs. PK: (patient_id, timestamp). Includes encrypted_payload, z_score, baselines table FK. 7-day TTL cleanup job. |
| `baselines` | Patient vital baselines. PK: (patient_id, hour_of_day). Recomputed weekly from vitals table. |
| `incoming_queue` | Hospital triage queue. Hospital UI queries this. Records patient name, vitals, dispatch_id, incoming_time. |
| `alerts` | Alert event log. PK: `alert_id`. Stores condition, severity, status (pending/dispatched/cancelled/completed). |
| `audit_log` | ATNA audit trail. PK: `id`. Immutable (INSERT only). Records patient_id, action, actor_id, timestamp, hash_chain field. Tamper-resistant via hash-chain trigger. |

### 5.2 Indexes

**Critical for SLA:**
- `idx_dispatches_created_at` — queries like "dispatches in last 24h".
- `idx_dispatches_alert_id` — lookup a dispatch by alert.
- `idx_vitals_patient_time` — aggregator queries `vitals WHERE patient_id = ? AND time > now() - 7 days`.
- `idx_baselines_patient_hour` — aggregator queries `baselines WHERE patient_id = ? AND hour_of_day = EXTRACT(hour FROM now())`.
- `idx_incoming_queue_hospital` — hospital UI queries `incoming_queue WHERE hospital_id = ?`.

**Maintenance:** weekly `ANALYZE` on key tables (automated via maintenance window). Monthly `REINDEX` to prevent bloat.

### 5.3 Migrations

Migrations are numbered `001` through `022+`. Run at container startup via `src/migrations/runner.js`. Runner checks `schema_migrations` table; applies only new ones (idempotent). Safe to run repeatedly.

**Key migrations:**
- `001–011`: Core schema (dispatches, patients, hospitals, vitals, audit).
- `012–016`: Auth model, staff credentials, hospital workflows.
- `017–019`: Patient PIN, PHR scans, compliance fields.
- `020`: Synthetic monitor seed data.
- `021`: ATNA hash-chain trigger + indexes.
- `022`: Hash-chain backfill for existing audit rows.

---

## 6. Testing Strategy

### 6.1 Test Types

| Type | Location | Command | Coverage |
|------|----------|---------|----------|
| **Unit** | `src/tests/vitals.test.js` | `npm run test:unit` | Vitals validation, deduplication. ~40 tests. |
| **Integration** | `src/tests/integration.test.js` | `npm run test:integration` | Ingestor → Postgres → Redis pub/sub. Mocked hospital/ambulance. |
| **Pipeline** | `src/tests/pipeline.integration.test.js` | `npm run test:pipeline` | End-to-end: SOS → ingestor → aggregator → detector → dispatcher → orchestrator. 45s timeout. Docker Compose stack required. Verifies latency SLA (< 5s). |
| **Services** | `src/tests/services.integration.test.js` | `npm run test:services` | Hospital integration, ambulance integration, staff-auth. 12 services, real Postgres. 30s timeout. |
| **Dispatcher** | `src/tests/test_*.js` | `npm run test:dispatcher` | Routing logic, retry behavior, SOS cancel, priority. 8 test files. |
| **Load** | `src/tests/load/` | k6 script (manual) | 1000 concurrent patients, 60s. Not run in CI. |

### 6.2 Coverage

**Current:** 60.8% line coverage (47 → 60 ratchet). Gate enforces ≥60%.

**Path to 75%:** routes (hospital, ambulance, patient UIs) via supertest, race-condition tests with `FOR UPDATE SKIP LOCKED`, dispatch state machine transitions.

**Pre-prod:** run load test at scale (1000+ concurrent). Measure: dispatch latency p99, CPU/memory usage, queue backlog. Target: p99 < 3s, CPU < 70%, queue empty after spike.

### 6.3 Manual Testing

No Selenium/Playwright suite yet (in TODO). Manual QA checklist:
1. Patient app: register → pair device → trigger SOS → see status updates.
2. Hospital UI: see incoming dispatch → accept → admit → handover.
3. Ambulance app: see dispatch → accept → GPS checkin → arrive → handover.
4. Paramedic watch: compact display of active dispatch + ETA.
5. Monitoring: Prometheus queries, Grafana dashboards update in real-time.

---

## 7. Deployment & Operations

### 7.1 Local Development

```bash
git clone https://github.com/TarunSitaraman/SmartResQ-dev.git
cd SmartResQ-dev
docker compose up --build -d
# Wait ~60s for services to be healthy
docker compose ps  # check all services are "Up"
```

All services available:
- Ingestor: http://localhost:3000/health
- Hospital UI: http://localhost:5303
- Ambulance UI: http://localhost:5304
- Patient UI: http://localhost:5301
- Monitoring: Prometheus http://localhost:9090, Grafana http://localhost:9003 (admin/admin)

### 7.2 Production Deployment

**Current topology:** single DigitalOcean droplet (`blr1`, Bangalore), Ubuntu 22.04, 1 vCPU/2 GB. Insufficient for real load.

**Deploy flow:**
```
Push to main
  ↓
GitHub Actions: security-gate (gitleaks, semgrep, npm audit, Trivy)
  ↓ (if passing)
Deploy job: capture current SHA → git pull → docker compose build → up -d → health check
  ↓ (if health check fails)
Automatic rollback: git reset --hard <captured SHA>
```

**Health check:** probes gateway `/health` + checks all 12 containers are `healthy` (via Docker health checks).

**Secrets:** stored in `/opt/smartresq/.env.prod` on DigitalOcean droplet. Never committed. Loaded at container startup. Startup guard rejects weak JWT_SECRET (< 32 chars) in prod mode.

**Monitoring:** Prometheus scrapes all services (`:9090`). Grafana queries Prometheus (`:9003`). Alertmanager sends critical alerts to Slack (not yet wired; in TODO).

### 7.3 Cloud Migration Path

**Ready to migrate:** IaC (Terraform) for AWS + Azure in `infra/` directory. Validated in CI.

**AWS (ap-south-1, Mumbai region):**
- RDS Postgres (multi-AZ)
- ElastiCache Redis
- ECS (container orchestration)
- ALB (load balancer)
- CloudWatch (monitoring)

**Azure (centralindia):**
- Azure Database for Postgres (HA)
- Azure Cache for Redis
- AKS (Kubernetes)
- Application Gateway (load balancer)
- Azure Monitor (monitoring)

**Migration effort:** 1–2 weeks (provisioning + testing + cutover). No code changes. Data residency verified by CI job (`check-india-residency.sh`).

### 7.4 Runbooks

Located in `deploy/runbooks/`:
- **rollback.md** — automatic rollback on deploy failure, manual rollback procedure.
- **backup-restore.md** — pg_dump to S3, restore time RTO ~3s (verified by drill 2026-06-14).
- **migrations.md** — how to add a new migration safely, testing locally.
- **incident-oncall.md** — triage flowchart, severity levels, communication escalation.

---

## 8. Security Posture

### 8.1 Threat Model

| Threat | Mitigation |
|--------|-----------|
| **Vitals in flight (man-in-the-middle)** | HTTPS only (TLS 1.3, enforced by nginx). Cloudflare SSL mode Full(Strict). HSTS preload. |
| **Vitals at rest** | AES-256-GCM encryption. Key stored in env (never on disk). Rotated on deploy. |
| **Patient ID leakage** | JWT auth on every endpoint. Patient can only query their own data (RBAC). No IDs in logs (masked). |
| **Dispatch decision manipulation** | Dispatcher validates alert schema. Orchestrator re-checks hospital capacity at time of decision (not cached). Fallback if no hospital available. |
| **Hospital EHR compromise** | SmartResQ doesn't store EHR data. Only sends FHIR bundle (pre-arrival context). EHR compromise doesn't leak SmartResQ patient data. |
| **Insider threat (admin reads all patient data)** | Audit log tamper-resistant (hash-chain). Admin access logged. Next owner should add break-glass audit alerts (admin read = alert to compliance). |
| **Accidental SQL injection** | Parameterized queries enforced. ESLint rule forbids string interpolation in DB. CI enforces. |
| **Supply chain (npm packages)** | npm audit (critical) gated in CI. Dependabot auto-updates (non-critical). SBOM generated on deploy (future). |
| **Replay attacks** | JWT includes `iat` (issued-at) and `exp` (expiry). Replay within 1-hour window possible (acceptable; HTTPS prevents interception). |

### 8.2 Compliance

- **DPDP (Digital Personal Data Protection Act)** — consent flag on patient record. Dispatch checks `dpdp_consent_given` before proceeding. Consent events logged.
- **SaMD (Software as Medical Device)** — regulatory classification needed. SmartResQ currently scoped as "emergency coordination," not medical diagnosis/treatment. Compliance service checks this on every dispatch.
- **HIPAA-equivalent** (for India) — not applicable in India, but architecture compatible. Audit log, encryption, RBAC are HIPAA-aligned.
- **ATNA** — audit trail compliant. Fire-and-forget events from all services. Immutable (INSERT only). Hash-chain for tamper-detection.

---

## 9. Observability & Monitoring

### 9.1 Logging

**Format:** JSON (pino), one line per log. Fields: `timestamp`, `level`, `service`, `message`, `traceId`, `error` (if applicable).

**Log destinations:** 
- **Local development:** stdout (docker compose logs).
- **Production:** Docker logs on DigitalOcean droplet. Rotated daily (logrotate config in infra/). Sent to CloudWatch/Azure Monitor (future; not wired yet).

**Sensitive data masking:** phone numbers masked to last 4 digits. Patient IDs keyed by dispatchId in vitals logs (no patient identity). Passwords never logged.

### 9.2 Metrics

**Prometheus format** (text format, scrape from `:9090` on each service).

**Key metrics:**
| Metric | Type | Used for |
|--------|------|----------|
| `alerts_processed_total` | Counter | Alert volume SLA. |
| `dispatch_decision_latency_ms` | Histogram | Dispatch speed. Alert on p99 > 5s. |
| `hospital_notification_latency_ms` | Histogram | Hospital response time. |
| `ambulance_notification_latency_ms` | Histogram | Ambulance response time. |
| `dispatch_queue_depth` | Gauge | Backlog monitor. Alert if > 100. |
| `vitals_ingested_total` | Counter | Vital volume per patient, per service. |
| `errors_total` | Counter | Error rate (by service, by endpoint). |
| `postgres_connection_pool` | Gauge | DB connection count. |
| `redis_queue_latency_ms` | Histogram | Redis publish-subscribe latency. |

**Grafana dashboards:**
1. **Dispatch SLA Dashboard** — p50/p99 latency, success rate, error reasons.
2. **Hospital Intake Dashboard** — incoming queue depth, average wait time, bed utilization.
3. **System Health Dashboard** — CPU, memory, disk, Postgres size, Redis memory, error rates.
4. **Audit Compliance Dashboard** — audit log write rate, failed dispatches (for investigation), hash-chain integrity.

### 9.3 Alerts

**Alertmanager rules** in `monitoring/alertmanager.yml`. Examples:
- Dispatch latency p99 > 5s → page oncall.
- Hospital notification timeout 3 times in 10 min → page oncall.
- Postgres disk space < 10% → alert ops.
- Redis memory > 1 GB → alert ops.
- Audit log hash-chain broken → page security.

**Notification:** Slack (wired in alertmanager config; token in `.env.prod`).

### 9.4 Synthetic Monitor

**Probe vital injected every 5 minutes.** Injected via `monitoring/synthetic-monitor/probe.js` as a cron job. Traces the probe through the entire pipeline: ingestor → aggregator → detector → dispatcher → orchestrator → hospital + ambulance.

**Verifies:**
- Dispatch latency (should be < 5s).
- Hospital and ambulance both notified.
- Dispatch record created.
- Audit events logged.

**Alerts if latency > 5s or steps missing.** Allows catch of regression immediately (not waiting for real patient).

---

## 10. Known Issues & TODO

### Critical (Go-Live Blockers)

None remaining (as of 2026-06-18). All security, reliability, audit, deployment gates are closed.

### High Priority (Post-Go-Live)

1. **Infrastructure scaling** — apply `infra/aws/` or `infra/azure/`. Migrate from single droplet to multi-AZ cloud.
2. **Staging tier** — set up staging environment, add smoke tests post-deploy.
3. **Load testing** — run k6 at 1000+ concurrent users. Measure CPU/memory under sustained load.
4. **Pen-test** — commission authorized pen-test before accepting real PHI.
5. **Non-root deploy user** — security best practice.

### Medium Priority

1. **STREAMS_ENABLED** — validate aggregator/detection consume from Redis Streams. Enable in prod for exactly-once guarantees.
2. **OTP enrollment** (#144) — SMS provider integration. `OTP_ENROLLMENT_REQUIRED` flag exists; wire SMS, flip flag.
3. **Push notifications** (#152) — send mobile push on dispatch + status updates. Job queued; awaiting SMS provider (shared dependency).
4. **Alertmanager Slack** — wire Slack token in `.env.prod`, test alerts.
5. **SBOM generation** — supply chain attestation on deploy.

### Low Priority (Nice-to-Have)

1. **Orchestrator UI** — command-center view of active dispatches. UI scaffolded; backend ready; incomplete.
2. **Selenium tests** — automate QA flows (register, SOS, hospital workflow).
3. **Multi-language support** — UIs currently EN only. Add TA (Tamil), HI (Hindi) translations (backend strings already i18n-ready).
4. **Analytics dashboard** — weekly dispatch volume, average latency trends, patient satisfaction scores.

---

## 11. Next Steps for New Owner

### Week 1: Familiarization

- [ ] Clone repo, run `docker compose up` locally. Trigger a full SOS → dispatch flow manually.
- [ ] Read `docs/SMARTRESQ_OVERVIEW.md` (legal product framing).
- [ ] Read `docs/CLOUD_DECISION_MATRIX.md` (cloud decision rationale).
- [ ] Walk through `pipeline.integration.test.js` — understand test coverage.
- [ ] Review `deploy/PRODUCTION.md` — understand current topology and gaps.

### Week 2: Deep Dive

- [ ] Trace a real SOS through code (ingestor → dispatcher → orchestrator → hospital). Add console logs, re-run pipeline test.
- [ ] Review security gates in `.github/workflows/` (gitleaks, semgrep, npm audit, Trivy).
- [ ] Review audit log schema + hash-chain verification in `src/migrations/021_atna_hash_chain.sql`.
- [ ] Understand Postgres connection pool + retry logic in `src/services/db.js`.
- [ ] Understand Redis pub/sub vs Streams toggle in `src/dispatcher.js`.

### Week 3: Readiness

- [ ] Run k6 load test locally (100 concurrent users, 60s). Measure latency, queue depth.
- [ ] Document current operational runbooks (SOP for on-call).
- [ ] Set up staging environment (if feasible) or document design.
- [ ] Plan infrastructure migration (AWS or Azure). Estimate timeline.
- [ ] Identify weak test coverage. Add tests for high-risk paths (orchestrator routing, fallback logic).

### Week 4: Ownership

- [ ] Close one high-priority issue (infrastructure OR load testing OR pen-test prep).
- [ ] Write incident runbook customized for your ops team.
- [ ] Set up Slack alerting (wire Alertmanager token).
- [ ] Onboard 1–2 hospital staff to staging environment. Collect feedback on UX.

---

## 12. Contact & Escalation

**Original architect:** @TarunSitaraman (GitHub). For architecture questions, blocking issues, or go-live readiness review.

**Issue tracking:** GitHub Issues tab. All decisions, known issues, feature requests are tracked there. Open issues are primarily #144 (OTP) and #152 (push notifications), both blocked on SMS provider integration.

**Code standards:** Enforced in CI. All PRs require 1 approval from `@TarunSitaraman` before merge. Branch protection on `main` enforces all security gates.

---

## 13. Architecture Decision Record Template

Use this template for future major decisions (e.g., scaling database, adding a new service, changing alert rules):

**Decision:** [one sentence — what choice is being made?]
**Context:** [why is this decision needed? what forces/constraints exist?]
**Options considered:** [at least 2, with pros/cons for each]
**Decision:** [which option won, and why? explicitly tie to constraints]
**Consequences:** [what becomes easier, what becomes harder, what debt is accepted?]
**Revisit condition:** [under what circumstance should this be revisited?]

Store decision record in `docs/ADR/` with filename `ADR-NNN-slug.md`.

---

## Summary

SmartResQ is a **production-grade emergency dispatch platform** with mature DevOps practices (automated security gates, proven disaster recovery, audit trail tamper-resistant, cloud-portable IaC). The engineering foundation is solid; path to scale is clear (apply Terraform IaC to migrate to AWS/Azure). Operational gaps are infrastructure (single-node DigitalOcean) and real-world testing (load test + pen-test before hospital PHI). New owner should focus on: (1) infrastructure migration, (2) staging tier setup, (3) real-world validation (load testing, pen-test, hospital pilot).

**Ready for hospital go-live after:** infrastructure upgrades, staging tier, load-testing pass, pen-test clearance, and operational onboarding of hospital staff (4–6 weeks from now, given current rate).

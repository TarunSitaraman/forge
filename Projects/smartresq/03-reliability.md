# SmartResQ Reliability Patterns

## Idempotent Dispatch

**Problem:** If dispatcher crashes mid-dispatch, alert might be processed twice.

**Solution:** Alert ID is unique key. Dispatch is upsert, not insert. Re-processing same alert is safe (updates dispatch record, re-sends notifications, no duplicate dispatch created).

**Implementation:** Dispatcher enqueues alert by ID. BullMQ treats job.name as unique within a queue. If same alert re-enqueued, job ID is same → no duplicate.

**Observability:** traceId on every request. Allows tail-tracing a single SOS through entire pipeline (ingestor → dispatcher → orchestrator → hospital/ambulance).

## Exactly-Once Alert Processing

**Problem:** If dispatcher crashes between consuming alert and processing it, alert is lost (pub/sub) or reprocessed (Streams).

**Solution:** Redis Streams with consumer groups + PEL (Pending Entry List) reclaim.

**How it works:**
1. Alert consumed from stream but not ack'd (xack).
2. Dispatcher crashes.
3. On restart, `reclaimPending()` walks PEL, finds crashed dispatcher's unack'd messages.
4. Reassigns messages to current dispatcher.
5. Dispatcher re-processes from known state (idempotent).

**Caveat:** Streams mode toggled via `STREAMS_ENABLED` flag (off by default). Next owner should verify aggregator/detection consume from Streams, then enable in prod.

**Current State:** Pub/sub mode (fire-and-forget). Acceptable for hospital deployment if orchestrator validates dispatch before sending notifications (no duplicate notification even if alert is replayed).

## Circuit Breakers

**All external calls wrapped in opossum circuit breaker.**

**Example:** Hospital EHR API timeout (8s cutoff) → circuit breaker fails open → fallback SMS sent → hospital still notified.

**Benefits:**
- Fails fast (no hanging).
- Cascading failures prevented.
- Fallback path automatic.
- Metrics tracked (opens, closes, half-open transitions).

**Configuration:** 5 consecutive failures → circuit open → fail-fast for 30s → half-open → test call → success → close or failures → re-open.

## Graceful Shutdown

**On SIGTERM (kubectl drain, docker stop, pm2 graceful restart):**

1. Server stops accepting new requests (returns 503).
2. In-flight requests drain for configurable timeout (default 10s).
3. BullMQ workers drain: process current job to completion or timeout.
4. Database connections closed.
5. Redis connections closed.
6. Process exits.

**Implementation:** `registerShutdown()` in `src/services/bootstrap.js`. Hooks process signals, waits for handlers.

**Result:** No orphaned jobs, no mid-dispatch crashes, no connection leaks.

## Race Condition Prevention

**Problem:** Two dispatchers might assign same bed to two patients.

**Solution:** `SELECT * FROM beds FOR UPDATE SKIP LOCKED` in orchestrator.

**How it works:**
1. Transaction starts.
2. Query beds for hospital with lock (FOR UPDATE).
3. SKIP LOCKED bypasses already-locked rows (another transaction has it).
4. Pick first available bed from unlocked rows.
5. Update bed assignment.
6. Commit.

**Result:** At most one dispatcher gets each bed. No duplicate assignments.

**Tested:** Race-condition integration test runs concurrent SOS on same hospital, verifies no duplicate bed assignments.

## Automatic Rollback on Deploy

**Problem:** Deploy a broken service → users affected.

**Solution:** Health check gate in deploy workflow.

**How it works:**
1. Push to main branch.
2. CI: security gates (gitleaks, semgrep, npm audit, Trivy).
3. CI: deploy job captures current SHA.
4. CI: git pull → docker compose build → docker compose up -d.
5. Health check: probe gateway `/health` + verify all containers healthy.
6. If health check fails → git reset --hard <captured SHA> → restart.

**Result:** Broken deploys auto-rollback in <2s. User downtime minimized.

## Deep Health Checks

**`GET /health` endpoint checks:**
- Postgres connectivity (SELECT 1 from health table).
- Redis connectivity (PING).
- BullMQ queue health (queue depth, worker status).
- Each service individually (hospital, ambulance, etc. integrations).

**Used by:** Docker health check (every 10s), Kubernetes readiness probe, monitoring dashboard.

**Result:** Degraded services detected before load reaches them.

## Distributed Tracing

**Every request assigned traceId at ingestor.**

**Logged at each service:**
- ingestor: receives request, assigns traceId.
- aggregator: processes vital from vitals-raw, logs traceId.
- detection: evaluates rules, logs traceId.
- dispatcher: enqueues job, logs traceId.
- orchestrator: dispatches, logs traceId.
- hospital/ambulance: notified, logs traceId.

**Observability:** Query logs by traceId, see entire SOS journey in order. Understand where latency happens.

**Endpoint:** `GET /trace/{traceId}` returns timeline of all services' processing times.

## Retry Logic

**BullMQ job retry: exponential backoff (1s, 2s, 4s).**

**Example:** Orchestrator call fails (network blip) → job retried after 1s → succeeds.

**After 3 retries:** Job moves to failed queue. Requires operator intervention (manual retry via Redis CLI or admin UI).

**Metrics:** Counter `dispatch_failed_total` tracks failed dispatches for alerting.

## Database Connection Pooling

**Postgres pool: 10 connections by default.**

**If all connections in use:** Subsequent requests queue (configurable timeout, default 30s). If queue fills or times out, request fails with 503.

**Monitored:** Gauge `postgres_connection_pool` tracks connection count. Alert if consistently > 8 (approaching limit).

## Subscription (Consent) Validation

**Before ingesting vital or dispatching:**

```javascript
if (!patient.subscription_active) {
  return 403 Forbidden;
}
if (!patient.dpdp_consent_given) {
  return 403 Forbidden;
}
```

**Result:** No orphaned dispatches for inactive patients.

## Metrics-Driven Reliability

**Key SLA metrics:**

| Metric | Target | Alert Threshold |
|--------|--------|-----------------|
| Dispatch latency (p99) | <5s | >5s page oncall |
| Hospital notification latency | <8s | timeout 3x in 10m → page |
| Ambulance notification latency | <5s | timeout 3x in 10m → page |
| Queue depth | <100 | >100 jobs → alert ops |
| Error rate | <1% | >1% → page |
| Postgres connection pool | <8 | >8 → alert ops |

**Synthetic monitor** injects probe vital every 5 min, verifies entire pipeline. Catches regressions before real patients affected.

See [Monitoring](./07-monitoring.md) for dashboard setup.

# SmartResQ Interview Preparation Guide

**Use this guide to interview for SmartResQ ownership or architect roles.**

---

## Elevator Pitch (2 minutes)

"SmartResQ is an emergency dispatch coordination system that connects patient emergencies (SOS or wearable vitals) to ambulances and hospitals in real-time. When a patient triggers an alert, we ingest vitals, score anomalies against patient baseline, apply alert rules, then route dispatch to the nearest hospital and ambulance simultaneously—all in under 5 seconds, with SMS/WhatsApp fallback if primary APIs timeout.

The engineering foundation is production-grade: automated security gates (gitleaks, semgrep, npm audit, Trivy), tamper-resistant audit trail, proven disaster recovery (RTO ~3s), and cloud-portable IaC (Terraform for AWS/Azure). Current blocker for hospital deployment is infrastructure (single-node DigitalOcean), not code quality. We're ready to scale to multi-AZ cloud."

---

## Architecture Explanation (5–7 minutes)

### High-Level Flow

```
Patient SOS
  ↓
Ingestor (validate, deduplicate, encrypt)
  ↓
Aggregator (score vs. baseline)
  ↓
Detection (apply alert rules)
  ↓
Dispatcher (queue dispatch job)
  ↓
Orchestrator (route to nearest hospital + ambulance)
  ↓
Parallel Notification (hospital + ambulance, not serial)
  ↓
Workflow Tracking & Audit
```

### Key Design Decision: Why Parallel?

**Problem:** If we notify hospital, wait for response, then notify ambulance, total latency = 8s + 5s = 13s. User perceives slow response.

**Solution:** Notify both simultaneously (Promise.all). Hospital starts prep while ambulance en route. Perceived latency = ambulance ETA only (~5 min), not sequential delays.

**Trade-off:** If hospital doesn't acknowledge, dispatch still recorded (no rollback). Fallback SMS ensures hospital gets notified anyway.

### Data Persistence Strategy

**Two write paths:**
1. **Postgres** — audit trail, query-able history, baselines, patient registry.
2. **Redis** — real-time pub/sub channels (vitals-raw, vitals-normalized, alerts) + BullMQ durable job queue.

**Why dual?** Postgres for compliance/audit, Redis for low-latency pub/sub. Allows dispatcher to retry failed jobs (BullMQ) without losing state.

### 5-Second SLA: How It's Achieved

1. **Fast path:** Alert → dispatcher → orchestrator → parallel notification = ~500ms typical.
2. **Circuit breakers:** External calls (EHR, CAD) fail fast, not cascade. Timeout 8s max (hospital), 5s max (ambulance).
3. **Idempotent dispatch:** Alert ID is unique key. Reprocessing same alert is safe (upsert, not insert).
4. **Graceful degradation:** If hospital EHR down, SMS fallback. If ambulance CAD down, WhatsApp fallback. Dispatch still completes.

---

## Difficult Questions & Answers

### Q: "What happens if your dispatcher crashes mid-dispatch?"

**Answer:** Good question. Depends on mode.

**Current (Redis pub/sub mode, default):**
- Alert consumed from Redis pub/sub but not yet processed.
- Dispatcher crashes.
- Alert is lost. No duplicate dispatch, but delay until synthetic monitor catches it (~5 min).

**Future (Redis Streams mode, disabled for stability):**
- Alert consumed from stream but not yet ack'd.
- Dispatcher crashes.
- On restart, `reclaimPending()` finds crashed dispatcher's unack'd messages.
- Reassigns messages to healthy dispatcher.
- Dispatcher re-processes from known state (idempotent → safe).

**Why Streams disabled?** Not yet verified that aggregator + detection consume from Streams. Next owner should verify end-to-end before enabling in prod.

### Q: "How do you prevent duplicate dispatches if the system retries?"

**Answer:** Idempotence via alert ID.

**Mechanism:**
```javascript
// Dispatcher enqueues alert by ID
await enqueueDispatch(alert); // BullMQ job.name = alertId

// If same alert re-processed:
// BullMQ job.name already exists → no duplicate job
// Dispatch upsert logic: INSERT INTO dispatches ... ON CONFLICT (alert_id) DO UPDATE
```

**Result:** Reprocessing same alert updates dispatch record (re-sends notifications), not creates duplicate.

### Q: "What if the hospital EHR API is down when we try to send the pre-arrival bundle?"

**Answer:** Fallback notification.

**Mechanism:**
1. Orchestrator calls hospital EHR endpoint (timeout: 8s).
2. If timeout or error (non-5xx assumed transient), fire fallback SMS to hospital admin.
3. Dispatch record still created (not failed).
4. Hospital gets notified via SMS even if EHR is down.

**Trade-off:** Duplicative notifications (patient may see SMS + app notification). Deduplication on receive (hospital staff should recognize duplicate).

### Q: "How do you handle race conditions (e.g., two dispatchers assign same bed)?"

**Answer:** Pessimistic locking with SKIP LOCKED.

**SQL:**
```sql
SELECT * FROM beds WHERE hospital_id = $1 AND available = true
FOR UPDATE SKIP LOCKED
LIMIT 1;
```

**How it works:**
1. Transaction A: locks first available bed (FOR UPDATE).
2. Transaction B: tries to query same beds, but SKIP LOCKED bypasses locked rows.
3. Transaction B: gets next available bed.
4. Result: no duplicate assignments.

**Tested:** Integration test with concurrent SOS on same hospital verifies no collisions.

### Q: "Your audit trail uses hash-chain. Isn't that overkill?"

**Answer:** Not for medical data.

**Rationale:** Audit log must be tamper-proof (regulatory requirement). Hash-chain provides cryptographic proof that logs haven't been altered post-hoc. If someone claims they never read patient data, audit trail with hash-chain proves otherwise (can't forge retroactively).

**Trade-off:** Slower verification (walk entire chain O(n)). But verification is read-path (not on critical path). Write is append-only (fast).

### Q: "What's your single point of failure?"

**Answer:** Currently, the Postgres instance (single node on DigitalOcean).

**Mitigation plan:**
1. Migrate to AWS RDS (multi-AZ) or Azure Database for Postgres (HA).
2. Multi-AZ means automatic failover if one AZ fails.
3. Read replicas for scalability.

**Why not done yet?** Single-node DigitalOcean is sufficient for code development + testing. Real deployment requires HA infrastructure (next owner's job).

### Q: "Your detection rules are hardcoded. How do you A/B test new rules?"

**Answer:** Currently, hardcoded in Python subprocess (not ideal).

**Next steps (low priority):**
1. Externalize rules to database (rule_id, condition, severity, suppression_if).
2. Load rules at startup (cache in memory).
3. Reload without restart (use SIGHUP signal or API endpoint).
4. A/B test: deploy new rule to staging, measure false-positive rate, then flip flag in prod.

**Why not yet?** Product discovery still in progress. Once rule set stabilizes, worth externalizing.

### Q: "Your monitoring uses Prometheus + Grafana. What if Prometheus is down?"

**Answer:** Good catch. Prometheus downtime = no metrics visibility, but dispatch continues working.

**Mitigation:**
1. Alerts are defined in Prometheus (if Prometheus down, alerts don't fire).
2. Recommended: Use managed Prometheus (AWS CloudWatch or Azure Monitor) with HA.
3. Alternatively, sidecar Alertmanager (stores rules separately, queries Prometheus when it's up).

**Current state:** Prometheus on single droplet (DigitalOcean). Not HA. Next owner should migrate to managed service.

---

## Design Tradeoffs (Architectural Decisions)

### 1. Redis Pub/Sub vs. Kafka

**Decision:** Redis pub/sub (default), Kafka as optional fallback.

**Rationale:**
- **Redis:** Low latency, simple, single-node deployment.
- **Kafka:** High throughput, persistence, multi-partition scalability.

**Trade-off:**
- Redis pub/sub = fire-and-forget (message loss if subscriber crashes).
- Kafka = durable (replicated, persisted), but higher latency + complexity.

**For hospital dispatch:** <5s SLA means low latency is critical. Redis is right choice for now. If throughput > Redis can handle, migrate to Kafka (parallel partition consumption).

### 2. Dual-Write (Postgres + Redis Streams)

**Decision:** Postgres for audit/query-ability, Redis Streams for exactly-once semantics.

**Rationale:**
- **Postgres:** Durability, ACID compliance, query language (SQL).
- **Streams:** Exactly-once consumer groups (no message loss on crash).

**Trade-off:**
- Dual-write = complexity (must keep both in sync, handle partial failures).
- Single source of truth = simpler, but less durable (Postgres-only) or less queryable (Streams-only).

**Why separate?** Postgres queries are slow for real-time consumption (disk I/O). Streams are fast (in-memory). Hybrid is best of both.

**Next owner:** Verify aggregator + detection actually consume from Streams, then enable Streams mode in prod.

### 3. Parallel Notification vs. Serial

**Decision:** Parallel (hospital + ambulance simultaneously).

**Rationale:** Perceived latency = max(hospital latency, ambulance latency), not sum. Reduces dispatch delay from 13s to 8s.

**Trade-off:**
- Parallel = complex error handling (what if one fails?). Dispatch still recorded (no rollback).
- Serial = simpler, but slow.

**Mitigated by:** Fallback notification (SMS/WhatsApp). Even if primary API fails, patient gets notified.

### 4. Encryption at Rest (AES-256-GCM)

**Decision:** Encrypt all PHI (vitals, medications, patient data).

**Rationale:** Regulatory requirement (DPDP in India, HIPAA-equivalent). Proof that SmartResQ takes security seriously.

**Trade-off:**
- Encryption = performance overhead (~5% latency impact, negligible for SLA).
- Decryption on every read (hospitals, relevance engine).

**Mitigated by:** Key rotation on deploy (prevents long-term key compromise). Encryption at rest, not in transit (TLS 1.3 handles transit).

---

## Scaling Questions (How Would You Scale This?)

### Scaling to 1M concurrent patients (instead of 4 test patients)

**Bottlenecks:**

1. **Ingestor (vitals ingestion)**
   - Current: Single Node.js process on 1 vCPU.
   - Scaled: Kubernetes pod replicas (auto-scale by CPU).
   - Vitals ingestion is stateless → horizontal scaling trivial.

2. **Aggregator + Detection (Python subprocesses)**
   - Current: Single process per Ingestor container.
   - Scaled: Separate services (Kubernetes deployments), scale independently.
   - Both subscribe to Redis channels → fan-out handles scale.

3. **Postgres (baselines queries)**
   - Current: Single instance on DigitalOcean (1 vCPU, 2GB).
   - Scaled: RDS multi-AZ + read replicas.
   - Aggregator queries read-only (baselines, vitals) → can use read replicas.
   - Detection writes (alert events) → primary only.

4. **Redis (pub/sub channels)**
   - Current: Single instance on DigitalOcean.
   - Scaled: Redis Cluster (sharding by key) or managed ElastiCache (AWS).
   - Pub/sub channels don't shard well (all subscribers need all messages).
   - Alternative: Kafka (partition by patient_id, each partition replicated).

5. **BullMQ queue depth**
   - Current: <100 jobs typical (dispatcher is fast).
   - Scaled: Monitor queue depth metric. If consistently > 1000, add more dispatcher worker processes (scale horizontally).
   - BullMQ supports multi-worker setup (all workers consume same Redis queue).

### Scaling to <1s SLA (instead of <5s)

**Optimizations:**

1. **In-memory vitals cache** (Aggregator)
   - Pre-load each patient's baseline into memory on startup.
   - Avoid Postgres query (latency = network + disk I/O).

2. **Redis Streams instead of pub/sub**
   - Streams = persistent queue (survives process crashes).
   - Enables parallel consumption (multiple aggregators on same stream partition).

3. **Geospatial indexing** (Orchestrator)
   - Use PostGIS extension to index hospital + ambulance locations.
   - Nearest-neighbor query in <10ms (instead of calculating distances for all).

4. **Circuit breaker tuning**
   - Fail fast (timeout 1s instead of 8s) if EHR is slow.
   - Fallback SMS immediately (don't wait for slow EHR).

5. **Co-locate services** (Kubernetes)
   - Use Pod affinity to schedule Orchestrator + Hospital Integration on same node.
   - Reduces network latency (localhost instead of inter-pod).

---

## Security Questions

### Q: "How do you prevent unauthorized access to patient data?"

**Answer:** RBAC (role-based access control) + JWT auth.

**Mechanism:**
1. Every HTTP endpoint validates JWT token.
2. JWT payload includes `role` (doctor, nurse, admin, dispatcher) and `hospital_id`.
3. Middleware checks: `if (patient.hospital_id !== user.hospital_id) return 403`.
4. Hospital staff can only view their own hospital's dispatches.

**Rate limiting:** Redis-backed, fail-closed. SOS endpoint: 15/hour. Vitals: 1000/hour.

### Q: "How do you prevent SQL injection?"

**Answer:** Parameterized queries only. ESLint rule forbids string interpolation.

**Example:**
```javascript
// Good
db.query('SELECT * FROM patients WHERE id = $1', [patientId]);

// Bad (linter error)
db.query(`SELECT * FROM patients WHERE id = ${patientId}`);
```

**Enforcement:** CI fails if rule violated.

### Q: "Your audit trail is immutable. Can you delete old records?"

**Answer:** No. By design.

**Mechanism:** Trigger on audit_log table forbids UPDATE and DELETE.

**Compliance:** Immutability proves no tampering (regulatory requirement). Old records are kept for full history.

**Retention:** 30-day backup (compliance requirement). Older records archived to cold storage (S3).

---

## "What Would You Improve Today?" (Self-Critique)

### 1. **Infrastructure (Biggest Gap)**
- Single-node DigitalOcean is unacceptable for real hospital load.
- **Fix:** Migrate to AWS (ap-south-1) or Azure (centralindia) using Terraform IaC (already ready).
- **Effort:** 2–3 weeks (provisioning + testing + cutover).

### 2. **Staging Tier (Operational Gap)**
- Current setup is prod-only. Deploys are high-risk (no staging to test against).
- **Fix:** Spin up staging environment (separate Postgres + Redis + services).
- **Effort:** 1 week (IaC already written, just apply to staging region).

### 3. **STREAMS_ENABLED (Reliability)**
- Redis Streams mode is disabled (exactly-once processing not verified).
- **Fix:** Verify aggregator + detection consume from Streams. Enable in staging. Load test. Enable in prod.
- **Effort:** 2–3 days (verification + testing).

### 4. **Alert Rules Hardcoded (Maintainability)**
- Detection rules are Python code. Adding/modifying requires redeploy.
- **Fix:** Externalize to database (rule table). Load at startup, reload without restart.
- **Effort:** 2–3 days (engineering).

### 5. **Load Testing Gap (Validation)**
- Current load test (k6 script) exists but never run at scale (1000+ concurrent users).
- **Fix:** Run k6 at scale. Measure CPU, latency, queue depth. Establish baseline.
- **Effort:** 1 day (execution) + 1 day (analysis + tuning).

### 6. **Pen-Test (Security)**
- Code audit automated (semgrep, gitleaks, npm audit). Real pen-test not yet commissioned.
- **Fix:** Engage authorized third-party. Full application + infrastructure audit.
- **Effort:** 2–4 weeks (parallel work).

### 7. **Monitoring Alerts (Operational)**
- Alertmanager configured but Slack not yet wired.
- **Fix:** Add Slack token to `.env.prod`. Test critical/warning alerts → channels.
- **Effort:** 1 day (setup).

---

## Final Questions to Ask Your Interviewer

(Use these if interviewing **for** a job at the company building SmartResQ)

1. **Infrastructure roadmap:** When do you plan to migrate from DigitalOcean to multi-AZ cloud?
2. **Hospital pilots:** Do you have signed commitments from hospitals? Timeline for first deployment?
3. **Regulatory:** Has a pen-test been commissioned? Who's handling DPDP/SaMD compliance?
4. **Scaling:** What's the target number of concurrent patients by end of 2026?
5. **Team:** Who will own this long-term? (Founder-led, hire someone, outsource to agency?)

---

## Summary

**SmartResQ is production-grade code with an infrastructure blocker.** Best hire would be someone who can:
- Migrate to AWS/Azure (use Terraform IaC).
- Establish staging tier + monitoring.
- Run load tests + pen-tests.
- Coordinate hospital pilots.
- Lead scaling to multi-AZ HA.

**Not suitable for:** Junior engineer (too much infrastructure knowledge required) or full-stack engineer (too specialized — need DevOps + backend expertise).

See complete documentation in [_index.md](./_index.md) and individual docs (Architecture, Reliability, Security, Operations, Testing, Monitoring, Components, Database, Roadmap).

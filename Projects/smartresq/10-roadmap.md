# SmartResQ Roadmap & Ownership Transition

## 4-Week Onboarding Plan (for New Owner)

### Week 1: Familiarization (4–6 hours)

**Goal:** Understand system at high level, run locally, review code structure.

- [ ] Clone repo and run `docker compose up`. Wait for all services healthy.
- [ ] Trigger a full SOS → dispatch flow manually (patient app → hospital intake → ambulance dispatch).
- [ ] Read [Overview](./01-overview.md) and [Architecture](./02-architecture.md).
- [ ] Walk through `src/tests/pipeline.integration.test.js` to understand happy path.
- [ ] Review `deploy/PRODUCTION.md` — understand current topology (single DigitalOcean) and gaps.
- [ ] Skim `docs/SMARTRESQ_OVERVIEW.md` (legal framing, scope boundaries).

**Deliverable:** Mental model of how SOS flows through system. Comfort running locally.

### Week 2: Deep Dive (6–8 hours)

**Goal:** Understand code, design decisions, reliability mechanisms.

- [ ] Trace a real SOS through code (add console.log, re-run pipeline test). Understand latency distribution.
- [ ] Review security gates in `.github/workflows/` (gitleaks, semgrep, npm audit, Trivy). Run locally: `npm run lint`.
- [ ] Review audit log schema + hash-chain verification in `src/migrations/021_atna_hash_chain.sql`. Test verification endpoint.
- [ ] Understand Postgres connection pool + retry logic in `src/services/db.js`.
- [ ] Understand Redis pub/sub vs Streams toggle in `src/dispatcher.js`. Why is Streams off by default?
- [ ] Review [Reliability](./03-reliability.md) and [Security](./04-security.md) documents.
- [ ] Review fallback logic in orchestrator (what happens if hospital EHR times out?).

**Deliverable:** Confidence in design rationale. Understanding of failure modes.

### Week 3: Readiness Assessment (8–10 hours)

**Goal:** Identify gaps, plan infrastructure upgrade, test under load.

- [ ] Run k6 load test locally: `k6 run src/tests/load/k6-script.js` (100 concurrent users, 60s). Measure latency p99, CPU, memory.
- [ ] Document current operational gaps (staging tier, monitoring alerts wired, incident runbooks).
- [ ] Set up staging environment locally (or document design if resources limited). Test smoke tests post-deploy.
- [ ] Plan infrastructure migration: AWS or Azure? Timeline? Estimated cost?
- [ ] Identify weak test coverage (< 60%). Plan additions (route handlers, race-condition tests, state machine).
- [ ] Review [Operations](./05-operations.md) and [Testing](./06-testing.md) documents.

**Deliverable:** Load test results. Infrastructure migration plan. Test coverage roadmap.

### Week 4: Ownership (8–10 hours)

**Goal:** Take first concrete action, establish operational rhythm.

- [ ] Close one high-priority issue:
  - Option A: Infrastructure scaling (provision AWS or Azure).
  - Option B: Staging tier (set up separate environment).
  - Option C: Load testing (run k6 at 1000+ concurrent, document results).
  - Option D: Pen-test prep (engage third-party security firm, get proposal).

- [ ] Write incident runbook customized for your ops team. Translate generic [Roadmap](./05-operations.md#incident-response) into team-specific procedures.

- [ ] Set up Slack alerting: wire Alertmanager token in `.env.prod`, test alert delivery.

- [ ] Onboard 1–2 hospital staff to staging (or mock environment). Collect UX feedback.

**Deliverable:** First shipped action (infrastructure, staging, or testing). Incident runbook. Alerting wired.

## Known Issues & Priority Work

### Critical (Go-Live Blockers) ✅ RESOLVED

None remaining as of 2026-06-18. All security, reliability, audit, and deployment gates are closed.

### High Priority (Post-Go-Live)

**Timeline:** Weeks 4–8 after ownership transition.

1. **Infrastructure scaling** (2–3 weeks effort)
   - Migrate from single DigitalOcean droplet to AWS (ap-south-1) or Azure (centralindia).
   - Use IaC (Terraform in `infra/` directory).
   - Test failover + disaster recovery.
   - **Blocker for real hospital deployment.** No production use without this.

2. **Staging tier** (1 week effort)
   - Separate environment for testing deploys.
   - Smoke tests run post-deploy.
   - Manual gate before prod deploy.

3. **Load testing at scale** (3–5 days effort)
   - Run k6 with 1000+ concurrent users.
   - Measure CPU, memory, queue depth under sustained load.
   - Target: p99 < 3s, CPU < 70%, no backlog after spike.
   - **Required before hospital PHI ingestion.**

4. **Pen-test** (2–4 weeks effort, parallel)
   - Engage authorized third-party.
   - Full application + infrastructure security audit.
   - Remediate findings.
   - **High liability — required before real hospital deployment.**

5. **Non-root deploy user** (1 day effort)
   - Create `deploy` user on DigitalOcean (or cloud host).
   - Grant docker group membership.
   - Re-key SSH.
   - Update deploy workflow.

### Medium Priority (During Hospital Ramp)

**Timeline:** Weeks 8–12.

1. **STREAMS_ENABLED toggle** (2–3 days)
   - Verify aggregator + detection consume from Redis Streams.
   - Enable in staging, run load test.
   - If stable, enable in prod.
   - **Benefit:** Exactly-once dispatch processing (zero message loss on crash).

2. **OTP enrollment** (Issue #144, 3–5 days)
   - Wire SMS provider (Twilio, AWS SNS, etc.).
   - Implement OTP flow (patient registration, phone verification).
   - Set `OTP_ENROLLMENT_REQUIRED = true` in prod.

3. **Push notifications** (Issue #152, 2–3 days)
   - Implement FCM (Firebase Cloud Messaging) or APNS (Apple).
   - Send mobile push on dispatch + status updates.
   - Job queued; awaiting SMS provider (shared dependency with OTP).

4. **Alertmanager Slack integration** (1 day)
   - Wire Slack token in `.env.prod`.
   - Test critical/warning alerts → Slack channels.
   - Set up on-call rotation.

5. **SBOM generation** (Supply chain attestation, 2–3 days)
   - Generate SBOM (Software Bill of Materials) on every deploy.
   - Store in S3 for compliance audits.
   - Publish to GitHub releases.

### Low Priority (Nice-to-Have)

**Timeline:** Post-go-live, as time allows.**

1. **Orchestrator UI** (Command-center view, 2–3 weeks)
   - Real-time dispatch dashboard (active, historical).
   - Dispatcher can re-route or escalate.
   - UI scaffolded; backend ready; incomplete.

2. **Selenium/Playwright tests** (QA automation, 3–4 weeks)
   - Automate manual QA flows (register → SOS → handover).
   - E2E browser tests.
   - Run nightly.

3. **Multi-language support** (i18n, 2–3 weeks)
   - Add Tamil (TA) and Hindi (HI) translations.
   - Backend strings already i18n-ready.
   - UIs currently EN-only.

4. **Analytics dashboard** (1–2 weeks)
   - Weekly dispatch volume, latency trends.
   - Patient satisfaction scores.
   - Hospital + ambulance performance metrics.

## Design Decisions to Revisit

**Conditions to reconsider current architecture:**

| Decision | Revisit If | Alternative |
|----------|-----------|-------------|
| **Redis pub/sub for real-time fan-out** | Latency > 5s consistently under load. | Kafka (higher throughput, persistence) or managed event stream. |
| **Single Postgres instance (current)** | Query latency > 100ms. Read replicas needed for scale. | RDS with read replicas or managed database. |
| **Hardcoded alert rules in detection** | Need to add/modify rules without deploy. | Externalize to database + cache. Reload without restart. |
| **7-day vitals window (memory)** | Memory usage > 2GB per patient. | Aggregate vitals to hourly summaries, prune older data. |
| **Hospital + ambulance parallel notification (no atomic consistency)** | Hospital says ambulance not notified. How to reconcile? | Add reconciliation job: query hospital + ambulance state every 10s, log discrepancies. |

## Metrics to Track

**Establish baseline post-go-live:**

| Metric | Current | Target | Measurement |
|--------|---------|--------|-------------|
| **Dispatch latency (p99)** | < 5s (synthetic) | < 3s (real load) | Prometheus histogram. Alert if > 5s. |
| **Success rate** | 100% (synthetic) | > 99.5% (real) | (dispatch_sent - dispatch_failed) / dispatch_sent. |
| **Hospital notification rate** | 100% (synthetic) | 100% (real) | hospital_notified / dispatch_sent. |
| **Ambulance notification rate** | 100% (synthetic) | 100% (real) | ambulance_notified / dispatch_sent. |
| **Error rate** | < 1% (synthetic) | < 1% (real) | errors_total / total_requests. |
| **Uptime** | N/A (single-node) | > 99.9% (HA) | Minutes down / total minutes. |
| **Audit log integrity** | 100% (hash-chain verified) | 100% | Daily audit verification endpoint. |

## Deployment Checklist (Before Hospital Go-Live)

- [ ] Infrastructure scaled (AWS or Azure, multi-AZ).
- [ ] Load test passed (1000+ concurrent, p99 < 3s, CPU < 70%).
- [ ] Staging tier deployed + smoke tests pass.
- [ ] Pen-test completed + findings remediated.
- [ ] Non-root deploy user configured.
- [ ] Incident runbooks customized for ops team.
- [ ] Alertmanager Slack wired.
- [ ] On-call rotation established.
- [ ] Hospital staff trained (pilot group).
- [ ] Backup + restore tested (RTO/RPO confirmed).
- [ ] Audit log verification working.
- [ ] Synthetic monitor alerting correctly.

**Estimated time to completion:** 4–6 weeks from ownership transition.

## Contact & Escalation

**Original architect:** @TarunSitaraman (GitHub)

**For architecture questions, blocking issues, or go-live readiness review: reach out.**

**All decisions documented in [Architecture Decision Records](./ADR/).** Store major decisions there. Use template in ADR-0000-template.md.

---

**See [Overview](./01-overview.md) for quick summary of where you are.**

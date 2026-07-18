# SmartResQ Overview

## What Is SmartResQ?

Real-time emergency dispatch system connecting patient emergencies (SOS or wearable anomalies) to ambulances and hospitals. Patient app → ingestor → aggregator (scoring) → detection (rules) → dispatcher (queue) → orchestrator (routing) → parallel notification (hospital + ambulance) → workflow tracking.

**Tech Stack:** Node.js + Python + React. Postgres + Redis. Docker Compose orchestration.

**Who Owns This:** Built by @TarunSitaraman. Designed for hospital deployment in India (compliance-ready for DPDP, ATNA audit).

## Maturity Level

**Code:** Production-grade.
- Automated security gates (gitleaks, semgrep, npm audit, Trivy) on every commit.
- Tamper-resistant audit trail (hash-chain verified).
- Disaster recovery tested (RTO ~3s, verified 2026-06-14).
- 60%+ code coverage with ratchet enforcement.

**Operations:** Single-node DigitalOcean droplet (1 vCPU/2 GB, Bangalore). **Insufficient for real load.** Architectural blocker for production, not code quality.

## Go-Live Readiness

**Blockers to hospital deployment:**
1. **Infrastructure** — migrate from single droplet to multi-AZ cloud (AWS/Azure IaC ready).
2. **Load testing** — run k6 at 1000+ concurrent users before accepting real PHI.
3. **Pen-test** — authorized security audit required (code audit automated; real testing pending).
4. **Staging tier** — current prod-only, no staging environment.

**Timeline:** 4–6 weeks from now (infrastructure migration + testing + hospital pilot).

## Key Constraint

**<5 second end-to-end dispatch time with fallback notification if primary channels are down or slow.**

This shapes every architectural decision: dual-write (Postgres + Redis), parallel notification (not serial), circuit breakers on external calls, graceful degradation (SMS fallback if EHR down).

## Ownership Transition Task

Understand:
- Architecture and decision rationale.
- Deployment model and operational gaps.
- Reliability posture (idempotency, exactly-once processing, graceful shutdown).
- Path to scale to multi-node cloud infrastructure.
- Security gates and audit trail mechanism.

Then:
1. Infrastructure migration (apply Terraform IaC).
2. Staging tier setup.
3. Load testing + pen-test.
4. Hospital staff onboarding.

## Key Stats

- **Services:** 14 (5 pipeline, 5 integration, 4 cross-cutting).
- **Dispatch SLA:** <5s p99.
- **Code Coverage:** 60.8%.
- **Security Gates:** Fully automated.
- **Audit Trail:** Tamper-resistant (hash-chain).
- **Cloud Portability:** Terraform for AWS + Azure, data-residency CI verified.

## Architecture Essence

```
Patient → Ingestor → Aggregator → Detection → Dispatcher → Orchestrator → {Hospital, Ambulance}
                                                               ↓
                                                        Parallel Notification
                                                        with SMS/WhatsApp Fallback
```

See [Architecture](./02-architecture.md) for full detail.

## Next: Onboarding Plan

[Roadmap](./10-roadmap.md) includes 4-week structured onboarding for new owner.

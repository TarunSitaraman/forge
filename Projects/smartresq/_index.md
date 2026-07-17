# SmartResQ Project Knowledge Pack

*Emergency response coordination platform connecting patients (SOS/vitals) → hospitals → ambulances in real-time.*

**Status:** Production-grade code, infrastructure-limited (single-node DigitalOcean). Ready for hospital deployment after infrastructure upgrade.

**Tags:** #status/active #type/project #stack/nodejs #stack/python #stack/postgres #stack/redis

**Key Constraint:** <5s end-to-end dispatch time with parallel notification fallback.

## Quick Navigation

- **[Overview](./01-overview.md)** — Maturity level, go-live readiness, ownership transition plan
- **[Architecture](./02-architecture.md)** — Mental model, core pipeline, service topology, design decisions
- **[Reliability](./03-reliability.md)** — Idempotency, circuit breakers, graceful shutdown, race-condition prevention
- **[Security](./04-security.md)** — Threat model, encryption, RBAC, audit trail, compliance
- **[Operations](./05-operations.md)** — Local dev, production topology, cloud migration path, runbooks
- **[Testing](./06-testing.md)** — Test strategy, coverage (60%), manual testing
- **[Monitoring](./07-monitoring.md)** — Logging, metrics, alerts, synthetic monitor
- **[Components](./08-components.md)** — Deep-dive: ingestor, dispatcher, aggregator, detection, orchestrator
- **[Database](./09-database.md)** — Schema, indexes, migrations, data model
- **[Roadmap](./10-roadmap.md)** — Known issues, high/medium/low priority work, 4-week onboarding plan

## Key Metrics

| Aspect | Status |
|--------|--------|
| **Code Coverage** | 60.8% (gate: ≥60%) |
| **Dispatch SLA** | <5s (p99) |
| **Security Gates** | Fully automated (gitleaks, semgrep, npm audit, Trivy) |
| **Audit Trail** | Tamper-resistant (hash-chain) |
| **Cloud Portability** | Ready (Terraform for AWS/Azure) |
| **Disaster Recovery** | Tested (RTO ~3s) |

## Architecture At a Glance

```
Patient Input (SOS/Wearables)
  ↓
Ingestor (validation, deduplication, encryption)
  ↓
Alert Pipeline (aggregator → detection → dispatcher)
  ↓
Dispatch Decision (nearest hospital + ambulance)
  ↓
Dual Notification (parallel, with fallback)
  ↓
Workflow Tracking & Audit
```

**Core Services:** 5 pipeline (ingestor, aggregator, detection, dispatcher, orchestrator) + 5 integration + 4 cross-cutting = 14 services.

**Data Layer:** Postgres + Redis. Postgres for audit/durability, Redis for real-time pub/sub + BullMQ queue.

## For New Owner

Start with [Roadmap](./10-roadmap.md) → Week 1 onboarding plan.

---

**Original architect:** @TarunSitaraman | **GitHub Issues:** Use for all decisions & tracking

---

## Related Systems / Reference

**If you're working on similar architecture:**
- [`Systems/Playbooks/deployment.md`](../../Systems/Playbooks/deployment.md) — Deployment workflow (similar to SmartResQ's CI/CD).
- [`Systems/Docs/Docker.md`](../../Systems/Docs/Docker.md) — Docker & Compose reference (SmartResQ uses docker-compose for local dev).
- [`Systems/Docs/Kubernetes.md`](../../Systems/Docs/Kubernetes.md) — Kubernetes concepts (next owner will scale to K8s).
- [`Systems/Docs/PostgreSQL.md`](../../Systems/Docs/PostgreSQL.md) — Postgres administration and performance tuning.
- [`Systems/Docs/LLMs.md`](../../Systems/Docs/LLMs.md) — LLM capabilities and pitfalls (relevant for future AI-driven dispatch optimization).
- [`Systems/Templates/Decision-Log.md`](../../Systems/Templates/Decision-Log.md) — Use for documenting architectural decisions (see ADR template below).

**If you're interviewing or onboarding:**
- [`INTERVIEW_GUIDE.md`](./INTERVIEW_GUIDE.md) — Reference material for technical interviews, difficult questions, design tradeoffs, and self-critique.

# SmartResQ Operations & Deployment

## Local Development

```bash
git clone https://github.com/TarunSitaraman/SmartResQ-dev.git
cd SmartResQ-dev
docker compose up --build -d
# Wait ~60s for all services to be healthy
docker compose ps
```

**All services available locally:**
- **Ingestor:** http://localhost:3000/health
- **Patient App UI:** http://localhost:5301
- **Hospital App UI:** http://localhost:5303
- **Ambulance App UI:** http://localhost:5304
- **Prometheus Metrics:** http://localhost:9090
- **Grafana Dashboards:** http://localhost:9003 (admin/admin)

**Test SOS flow:** Patient app → register → pair device → trigger SOS → see real-time status updates in hospital + ambulance UIs.

## Current Production Topology

**Single DigitalOcean droplet:** blr1 (Bangalore), Ubuntu 22.04, 1 vCPU / 2 GB RAM.

**Limitations:** Cannot handle real hospital load. Architectural blocker (not code quality issue).

**Stack on droplet:**
- 12+ Node.js/Python services (docker compose).
- Postgres 16 (single instance, no replication).
- Redis 7.2 (single instance, no replication).
- Prometheus + Grafana (monitoring).
- Nginx (reverse proxy, TLS termination).

**Ingress:** Cloudflare → Nginx (:80, :443 TLS) → Docker network.

**Secrets:** `/opt/smartresq/.env.prod` (encrypted at rest, SSH-restricted access).

## Deploy Flow

```
Push to main
  ↓
GitHub Actions: Security gates
  ├─ gitleaks (no secrets)
  ├─ semgrep (no code defects)
  ├─ npm audit (no critical vulns)
  └─ trivy (no image vulns)
  ↓ (if all pass)
Deploy job:
  ├─ Capture current SHA
  ├─ git pull
  ├─ docker compose build
  ├─ docker compose up -d
  └─ Health check probe
      ├─ GET /health on all services
      └─ Verify Docker containers healthy
  ↓ (if health check fails)
Automatic rollback:
  ├─ git reset --hard <captured SHA>
  └─ docker compose up -d
```

**Result:** Broken deploys auto-rollback in <2s. User downtime < 30s.

**Health check:** Probes `/health` endpoint (checks Postgres, Redis, queue health). Repeats every 10s for 2 minutes. If any check fails, triggers rollback.

## Cloud Migration Path

**Ready to execute. IaC (Terraform) for AWS + Azure in `infra/` directory.**

### AWS (ap-south-1, Mumbai)

```
RDS Postgres (multi-AZ, read replicas)
ElastiCache Redis (cluster mode, multi-AZ)
ECS (Fargate, auto-scaling by CPU/memory)
ALB (load balancer, health checks)
CloudWatch (centralized logging + metrics)
S3 (backup bucket, cross-region replication)
```

**Estimated effort:** 1–2 weeks (provisioning + testing + cutover). Zero code changes.

### Azure (centralindia)

```
Azure Database for Postgres (HA, geo-redundancy)
Azure Cache for Redis (Premium tier, geo-replication)
AKS (Kubernetes, auto-scaling)
Application Gateway (load balancer)
Azure Monitor (centralized observability)
Blob Storage (backup)
```

**Estimated effort:** 1–2 weeks (provisioning + testing + cutover). Zero code changes.

### Data Residency Verification

**CI job `check-india-residency.sh` verifies all infrastructure in India.**

**Checks:**
- AWS region must be ap-south-1 (Mumbai).
- Azure region must be centralindia.
- No egress to non-India endpoints.

**Purpose:** DPDP compliance (personal data stays in India).

## Runbooks (in `deploy/runbooks/`)

### Rollback

```bash
# Manual rollback to previous version
git log --oneline -5  # find commit to rollback to
git reset --hard <commit-sha>
docker compose up -d
docker compose logs -f  # verify all services healthy
```

**Time to rollback:** ~2 minutes (git + docker restart).

### Backup & Restore

**Backup procedure:**
```bash
pg_dump $DATABASE_URL | gzip > backup-$(date +%Y%m%d-%H%M%S).sql.gz
# Upload to S3 (configured in infra/)
```

**Restore procedure:**
```bash
gunzip < backup-20260717-120000.sql.gz | psql $DATABASE_URL
```

**RTO (Recovery Time Objective):** ~3 seconds (verified by drill 2026-06-14). pg_dump: 1.2s, restore: 1.7s.

**RPO (Recovery Point Objective):** Hourly (backup job runs every hour).

### Migrations

**Adding a new migration:**

1. Create file: `src/migrations/023_new_schema.sql`.
2. Write migration (idempotent, can be run multiple times safely).
3. Test locally: `docker compose exec postgres psql $DATABASE_URL -f src/migrations/023_new_schema.sql`.
4. Verify rollback works (idempotent DOWN migration, or recreate schema locally).
5. Push to main. Deploy job runs migration at startup (migration runner in `src/migrations/runner.js`).

**Idempotent pattern:**
```sql
CREATE TABLE IF NOT EXISTS new_table (
  id BIGSERIAL PRIMARY KEY,
  ...
);

INSERT INTO schema_migrations VALUES ('023_new_schema.sql')
ON CONFLICT DO NOTHING;
```

**Result:** Safe to deploy. Migration runs once, even if deployed multiple times.

### Incident Response

**Flowchart in `deploy/runbooks/incident-oncall.md`.**

**Severity levels:**
- **SEV1 (Critical):** Dispatch latency > 10s or no dispatches for 5+ minutes → page oncall immediately.
- **SEV2 (High):** Error rate > 5% or hospital notification timeout → alert ops, investigate within 30 min.
- **SEV3 (Medium):** Deployment failures, audit log lag → log, plan fix for next sprint.

**Escalation:** If oncall doesn't respond in 5 min, escalate to @TarunSitaraman.

**Communication:** Slack channel `#smartresq-incidents`. Post incident summary (impact, root cause, resolution, prevention).

## Secrets Rotation

**Current:** Secrets in `/opt/smartresq/.env.prod` (manual management).

**Next owner should:**
1. Migrate to AWS Secrets Manager / Azure KeyVault.
2. Automate rotation (change secret in vault, services pick up via API on next startup).
3. Document rotation SOP (how to generate new JWT_SECRET, etc.).

**Validation:** Startup guard rejects weak secrets in prod mode. Prevents accidental deployment of dev keys.

## Monitoring Infrastructure

**Prometheus:** Scrapes all services (:9090) every 15s. Metrics stored in TSDB (default 15d retention).

**Grafana:** Queries Prometheus. Dashboards for SLA, hospital intake, system health, audit compliance.

**Alertmanager:** Sends alerts to Slack on critical events (not yet wired; in TODO).

**Synthetic Monitor:** Injects probe vital every 5 min. Traces entire pipeline. Alerts if latency > 5s or steps missing.

See [Monitoring](./07-monitoring.md) for metrics + dashboards.

## Deployment Checklist

Before hospital go-live:
- [ ] Infrastructure scaled to AWS/Azure (multi-AZ).
- [ ] Load test passed (1000+ concurrent users, p99 < 3s).
- [ ] Staging tier deployed + smoke tests pass.
- [ ] Pen-test completed (authorized third-party).
- [ ] Incident runbooks customized for ops team.
- [ ] Hospital staff trained (pilot group).
- [ ] On-call rotation established.
- [ ] Alertmanager Slack channel wired.

Estimated time to completion: 4–6 weeks.

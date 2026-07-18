# SmartResQ Monitoring & Observability

## Logging

**Format:** JSON (pino library), one line per log.

```json
{
  "timestamp": "2026-07-17T12:34:56.789Z",
  "level": "info",
  "service": "dispatcher",
  "message": "dispatch_queued",
  "traceId": "trace-abc123",
  "alertId": "alert-456",
  "dispatchId": "dispatch-789"
}
```

**Fields per service:**
- `timestamp` — ISO 8601.
- `level` — debug, info, warn, error.
- `service` — ingestor, dispatcher, aggregator, etc.
- `message` — short description of event.
- `traceId` — unique ID tracking SOS through entire pipeline.
- `duration_ms` — latency (for performance events).
- `error` — stack trace (for errors only).

**Destinations:**
- **Development:** stdout (visible in `docker compose logs`).
- **Production:** Docker logs on DigitalOcean droplet. Rotated daily via logrotate. Future: centralized to CloudWatch/Azure Monitor.

**Sensitive data masking:**
- Phone numbers → last 4 digits only (e.g., `+91-XXXX-3210`).
- Patient IDs → replaced with dispatchId in vitals logs (no identity).
- Passwords, OAuth tokens → never logged.

**Tail-tracing:** Query logs by traceId to follow a single SOS through entire pipeline:
```bash
docker compose logs -f | grep trace-abc123
# Output shows all services' processing in order
```

## Metrics (Prometheus)

**Scrape interval:** 15 seconds (all services expose metrics on `/metrics` endpoint).

**Retention:** Default 15 days (configurable).

**Metric types:**

### Counter (monotonic, always increasing)

| Metric | Meaning |
|--------|---------|
| `alerts_processed_total` | Total alerts received since startup. |
| `dispatch_sent_total` | Total dispatches issued. |
| `hospital_notified_total` | Total hospital notifications sent. |
| `ambulance_notified_total` | Total ambulance notifications sent. |
| `vitals_ingested_total` | Total vitals received (by patient, by service). |
| `errors_total` | Total errors (by service, by endpoint). |

### Histogram (distribution of values, p50/p99)

| Metric | Meaning |
|--------|---------|
| `dispatch_decision_latency_ms` | Time from alert received to dispatch decision. Target: p99 < 5s. |
| `hospital_notification_latency_ms` | Time from dispatch to hospital acknowledgment. |
| `ambulance_notification_latency_ms` | Time from dispatch to ambulance acknowledgment. |
| `vitals_ingestion_latency_ms` | Time from vital received to Postgres stored. |
| `redis_queue_latency_ms` | Time from published to consumed (pub/sub latency). |
| `postgres_query_latency_ms` | Database query execution time (by query type). |

### Gauge (instantaneous value)

| Metric | Meaning |
|--------|---------|
| `dispatch_queue_depth` | Number of pending dispatch jobs in BullMQ. Alert if > 100. |
| `postgres_connection_pool` | Active connections to database. Alert if > 8. |
| `redis_memory_usage_bytes` | Memory used by Redis. Alert if > 1GB. |
| `postgres_table_size_bytes` | Size of key tables (vitals, audit_log). Monitor for growth. |
| `active_dispatches` | Number of in-flight dispatches (state != completed). |

## Grafana Dashboards

**Grafana running on localhost:9003 (admin/admin).**

### Dashboard 1: Dispatch SLA

**Panels:**
- **Dispatch latency (p50, p99)** — histogram of dispatch_decision_latency_ms. Target line at 5s.
- **Success rate** — (dispatch_sent_total - dispatch_failed_total) / dispatch_sent_total * 100. Target: > 99%.
- **Hospital notification rate** — hospital_notified_total / dispatch_sent_total * 100. Target: 100%.
- **Ambulance notification rate** — ambulance_notified_total / dispatch_sent_total * 100. Target: 100%.
- **Error breakdown** — errors_total by endpoint (which routes are failing?).
- **Alert volume** — alerts_processed_total over time (trend).

**Frequency:** Updated every 15s (Prometheus scrape interval).

### Dashboard 2: Hospital Intake

**Panels:**
- **Incoming queue depth** — number of pending patients (from incoming_queue table). Real-time.
- **Average wait time** — from dispatch to hospital acknowledgment. Target: < 8s.
- **Bed utilization** — active_beds / total_beds per hospital (gauge query).
- **Handover completion time** — from admission to handover. Target: < 30 min for routine cases.

**Data source:** Postgres queries (supplemented by Prometheus metrics).

### Dashboard 3: System Health

**Panels:**
- **CPU usage** — per service (docker container CPU %).
- **Memory usage** — per service (docker container memory %).
- **Disk usage** — Postgres + Redis data directories.
- **Postgres connection pool** — active / total. Alert if > 8.
- **Redis memory** — used / max. Alert if > 1GB.
- **Error rate** — errors_total / (alerts_processed_total + vitals_ingested_total). Target: < 1%.

### Dashboard 4: Audit Compliance

**Panels:**
- **Audit log write rate** — events/min. Verify continuous logging.
- **Failed dispatches (by reason)** — dispatch_failed_total grouped by failure reason. For investigation.
- **Hash-chain integrity** — result of audit verification endpoint. Should be "Verified" (no tampering).
- **Admin actions** — count of admin role actions (for break-glass detection).

## Alertmanager Rules

**Located in `monitoring/alertmanager.yml`.**

**Example rules:**

```yaml
alert: DispatchLatencyHigh
expr: dispatch_decision_latency_ms{quantile="0.99"} > 5000
for: 5m
labels:
  severity: critical
annotations:
  summary: "Dispatch latency p99 exceeding 5s"
  action: "Page oncall immediately"

alert: HospitalNotificationFailure
expr: (hospital_notified_total - hospital_notified_total offset 10m) == 0
for: 2m
labels:
  severity: critical
annotations:
  summary: "No hospital notifications for 10 minutes"
  action: "Page oncall, check hospital-integration service"

alert: PostgresConnectionPoolWarning
expr: postgres_connection_pool > 8
for: 2m
labels:
  severity: warning
annotations:
  summary: "Database connection pool approaching limit"
  action: "Alert ops, investigate slow queries"
```

**Notification channels:**
- **Critical:** Slack `#smartresq-incidents` + page oncall.
- **Warning:** Slack `#smartresq-ops` + ops channel.

**Retry:** If not acknowledged in 30 min, escalate (email, SMS if configured).

## Synthetic Monitor

**Probe injected every 5 minutes via cron job in `monitoring/synthetic-monitor/probe.js`.**

**What it does:**
1. Creates test patient (if not exists).
2. Triggers synthetic SOS.
3. Measures time to dispatch decision (should be < 5s).
4. Verifies hospital notification received.
5. Verifies ambulance notification received.
6. Verifies dispatch record created.
7. Verifies audit events logged.

**Alerts if:**
- Latency > 5s → alert ops (potential regression).
- Any step missing → alert ops (pipeline broken).
- More than 1 failure in 15 min → page oncall (severity escalation).

**Tracing:** All synthetic events tagged with `synthetic=true` in logs. Allows filtering from real alerts.

**Benefits:** Catch regressions immediately (not waiting for real patient to discover).

## Correlation IDs (traceId)

**Every request assigned unique traceId at ingestor.**

**Logged at each hop:**
```
ingestor: received POST /alerts/sos (traceId: trace-abc123)
dispatcher: consumed alert (traceId: trace-abc123)
orchestrator: dispatched (traceId: trace-abc123)
hospital-integration: notification received (traceId: trace-abc123)
ambulance-integration: notification received (traceId: trace-abc123)
```

**Tail-trace endpoint:**
```
GET /trace/trace-abc123

Response:
{
  "traceId": "trace-abc123",
  "steps": [
    { "service": "ingestor", "event": "received", "timestamp": "12:34:56.100", "duration_ms": 10 },
    { "service": "dispatcher", "event": "enqueued", "timestamp": "12:34:56.150", "duration_ms": 50 },
    { "service": "orchestrator", "event": "dispatched", "timestamp": "12:34:56.200", "duration_ms": 5 },
    { "service": "hospital-integration", "event": "notified", "timestamp": "12:34:56.300", "duration_ms": 100 },
    { "service": "ambulance-integration", "event": "notified", "timestamp": "12:34:56.350", "duration_ms": 50 }
  ],
  "total_latency_ms": 250
}
```

**Benefits:** Understand where latency happens. Debug outliers.

## Deep Health Checks

**`GET /health` endpoint returns JSON:**

```json
{
  "status": "healthy",
  "timestamp": "2026-07-17T12:34:56Z",
  "services": {
    "postgres": { "status": "healthy", "latency_ms": 2 },
    "redis": { "status": "healthy", "latency_ms": 1 },
    "bullmq": { "status": "healthy", "queue_depth": 3, "workers_active": 1 },
    "hospital-integration": { "status": "healthy", "latency_ms": 50 },
    "ambulance-integration": { "status": "healthy", "latency_ms": 45 }
  }
}
```

**Used by:** Docker health check (every 10s), Kubernetes readiness probe, monitoring dashboard.

**Degraded service detection:** If any sub-service unhealthy, overall health = degraded (HTTP 503). Kubernetes stops routing traffic.

## Local Monitoring Setup

**Start stack:**
```bash
docker compose up -d
```

**Access:**
- Prometheus: http://localhost:9090
- Grafana: http://localhost:9003 (admin/admin)

**Query examples (Prometheus):**
```
# Dispatch latency p99
histogram_quantile(0.99, dispatch_decision_latency_ms)

# Error rate
rate(errors_total[5m]) / rate(alerts_processed_total[5m])

# Queue depth
dispatch_queue_depth

# Connection pool
postgres_connection_pool
```

**Grafana: Add Prometheus as data source (http://localhost:9090) → create panels with queries above.**

## Production Monitoring Checklist

Before hospital go-live:
- [ ] All Grafana dashboards configured and tested.
- [ ] Alertmanager rules tuned (no alert fatigue, no missed alerts).
- [ ] Slack channel wired for notifications.
- [ ] On-call rotation setup (who responds to which alerts).
- [ ] Synthetic monitor running and alerting correctly.
- [ ] Log retention policy defined (daily rotation, 30-day archive).
- [ ] Centralized logging configured (CloudWatch or Azure Monitor).
- [ ] Audit log verification script tested (detects tampering).

See [Roadmap](./10-roadmap.md) for timeline.

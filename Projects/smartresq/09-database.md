# SmartResQ Database Schema

## Core Tables

| Table | PK | Role |
|-------|----|----|
| `patients` | `patient_id` (VARCHAR 60) | Patient registry. Includes ABHA ID, subscription, DPDP consent flag. |
| `patient_devices` | `device_id` (VARCHAR 60) | Wearable enrollments. FK: `patient_id`. Device brand, model, OAuth token (encrypted), last sync. |
| `hospitals` | `hospital_id` (VARCHAR 60) | Hospital registry. Coordinates, specialties, bed capacity, EHR endpoint. |
| `ambulances` | `ambulance_id` (VARCHAR 60) | Ambulance registry. Crew, base station, vehicle status. |
| `dispatches` | `dispatch_id` (VARCHAR 60) | **KEY TABLE.** Dispatch audit trail. Alert ID, ambulance ID, hospital ID, latencies, fallback methods, completion status. |
| `vitals` | (patient_id, timestamp) | Raw vital signs. Encrypted payload, z-score, baseline FK. 7-day TTL cleanup. |
| `baselines` | (patient_id, hour_of_day) | Patient vital baselines. Recomputed weekly. Fallback to global if < 100 samples. |
| `incoming_queue` | `id` (BIGSERIAL) | Hospital triage queue. Patient name, vitals, dispatch_id, arrival time. |
| `alerts` | `alert_id` (VARCHAR 100) | Alert event log. Condition, severity, status (pending/dispatched/cancelled/completed). |
| `audit_log` | `id` (BIGSERIAL) | ATNA audit trail. Immutable (INSERT only). Patient ID, action, actor ID, timestamp, hash_chain. |
| `emergency_contacts` | `id` (BIGSERIAL) | Family members. FK: patient_id. Name, phone, relationship, consent flags (SMS, WhatsApp, GPS tracking). |
| `subscriptions` | `subscription_id` (VARCHAR 60) | Billing. Plan type, monthly cost, Razorpay ID, auto-renew flag, payment status. |

## Key Indexes (Critical for SLA)

```sql
-- Dispatch queries
CREATE INDEX idx_dispatches_created_at ON dispatches(created_at DESC);
CREATE INDEX idx_dispatches_alert_id ON dispatches(alert_id);
CREATE INDEX idx_dispatches_hospital ON dispatches(hospital_id);
CREATE INDEX idx_dispatches_ambulance ON dispatches(ambulance_id);

-- Vitals queries
CREATE INDEX idx_vitals_patient_time ON vitals(patient_id, recorded_at DESC);
CREATE INDEX idx_vitals_patient_score ON vitals(patient_id, z_score DESC);

-- Baselines queries
CREATE INDEX idx_baselines_patient_hour ON baselines(patient_id, hour_of_day);

-- Hospital intake queries
CREATE INDEX idx_incoming_queue_hospital ON incoming_queue(hospital_id, created_at DESC);

-- Audit queries
CREATE INDEX idx_audit_log_patient ON audit_log(patient_id, created_at DESC);
CREATE INDEX idx_audit_log_timestamp ON audit_log(created_at DESC);
```

**Maintenance:** Weekly `ANALYZE` on key tables. Monthly `REINDEX` to prevent bloat.

## Data Model Specifics

### Patients Table

```sql
CREATE TABLE patients (
  patient_id           VARCHAR(60) PRIMARY KEY,
  abha_id              VARCHAR(30) UNIQUE,        -- 14-digit ABHA number (Indian universal health ID)
  full_name            VARCHAR(120) NOT NULL,
  date_of_birth        DATE,
  sex                  VARCHAR(10),                -- 'M' | 'F' | 'O'
  phone                VARCHAR(20),
  email                VARCHAR(100),
  language             VARCHAR(10) DEFAULT 'EN',  -- 'EN' | 'TA' | 'HI'
  weight_kg            NUMERIC(5,1),
  height_cm            NUMERIC(5,1),
  known_conditions     TEXT[] DEFAULT '{}',        -- ICD-10 codes
  allergies            TEXT[] DEFAULT '{}',
  medications          JSONB DEFAULT '[]',
  subscription_plan    VARCHAR(30) DEFAULT 'free', -- 'free' | 'individual' | 'family' | 'corporate'
  subscription_active  BOOLEAN DEFAULT false,
  subscription_expiry  DATE,
  emergency_window_sec INTEGER DEFAULT 30,         -- SOS timeout
  abha_linked          BOOLEAN DEFAULT false,
  dpdp_consent_given   BOOLEAN DEFAULT false,
  dpdp_consent_ts      TIMESTAMPTZ,
  created_at           TIMESTAMPTZ DEFAULT NOW(),
  updated_at           TIMESTAMPTZ DEFAULT NOW()
);
```

### Dispatches Table

```sql
CREATE TABLE dispatches (
  dispatch_id             VARCHAR(60) PRIMARY KEY,
  alert_id                VARCHAR(100) NOT NULL,
  patient_id              VARCHAR(60) REFERENCES patients(patient_id),
  
  -- Hospital routing
  hospital_id             VARCHAR(60) REFERENCES hospitals(hospital_id),
  hospital_resource_id    VARCHAR(100),
  hospital_triage_level   VARCHAR(10),
  hospital_bed_assignment VARCHAR(120),
  hospital_ack_time       TIMESTAMPTZ,
  hospital_latency_ms     INTEGER,
  hospital_fallback       VARCHAR(20),    -- 'sms' if EHR timeout
  hospital_status         VARCHAR(50),    -- 'notified' | 'acknowledged' | 'admitted' | 'completed'
  
  -- Ambulance routing
  ambulance_id            VARCHAR(60) REFERENCES ambulances(ambulance_id),
  emri_dispatch_id        VARCHAR(100),
  emri_eta                VARCHAR(50),
  ambulance_ack_time      TIMESTAMPTZ,
  ambulance_latency_ms    INTEGER,
  ambulance_fallback      VARCHAR(20),    -- 'whatsapp' if CAD timeout
  ambulance_status        VARCHAR(50),    -- 'notified' | 'accepted' | 'arrived' | 'handover'
  crew_id                 VARCHAR(50),
  
  -- Contact notifications
  contact_delivery_status TEXT,           -- JSON array of per-contact results
  
  -- SLA tracking
  total_latency_ms        INTEGER,
  state                   VARCHAR(30),    -- 'PENDING' | 'AMBULANCE_ASSIGNED' | 'ARRIVED' | 'HOSPITAL_RECEIVED' | 'HANDOVER_COMPLETED' | 'CLOSED'
  
  created_at              TIMESTAMPTZ DEFAULT NOW(),
  updated_at              TIMESTAMPTZ DEFAULT NOW(),
  completed_at            TIMESTAMPTZ
);
```

**This is the key table for SLA tracking and auditing.** Every dispatch journey from alert to completion is logged here.

### Vitals Table

```sql
CREATE TABLE vitals (
  id                  BIGSERIAL PRIMARY KEY,
  patient_id          VARCHAR(60) REFERENCES patients(patient_id),
  vital_type          VARCHAR(30),         -- 'HR' | 'SPO2' | 'BP' | 'TEMP' | 'RR'
  value               NUMERIC(8,2),
  unit                VARCHAR(20),
  encrypted_payload   BYTEA,               -- AES-256-GCM, includes nonce
  z_score             NUMERIC(6,3),        -- computed by aggregator
  recorded_at         TIMESTAMPTZ DEFAULT NOW(),
  created_at          TIMESTAMPTZ DEFAULT NOW(),
  
  -- TTL: cleanup job deletes vitals > 7 days old (daily, configurable)
  INDEX idx_vitals_patient_time (patient_id, recorded_at DESC),
  INDEX idx_vitals_created (created_at DESC)
);
```

**7-day TTL:** Cleanup job runs daily, prunes vitals > 7 days old. Aggregator keeps 7-day rolling window for trend detection (in memory).

### Baselines Table

```sql
CREATE TABLE baselines (
  id              BIGSERIAL PRIMARY KEY,
  patient_id      VARCHAR(60) REFERENCES patients(patient_id),
  vital_type      VARCHAR(30),    -- 'HR' | 'SPO2' | etc.
  hour_of_day     INTEGER,        -- 0-23 (time-of-day profile)
  mean            NUMERIC(8,2),
  stddev          NUMERIC(8,2),
  sample_count    INTEGER,        -- must be >= 100 for confidence
  updated_at      TIMESTAMPTZ DEFAULT NOW()
);
```

**Recomputed weekly** from vitals table. If sample_count < 100, aggregator falls back to global baseline. This allows personalized anomaly detection after patient accrues 7 days of vitals.

### Audit Log Table (ATNA)

```sql
CREATE TABLE audit_log (
  id              BIGSERIAL PRIMARY KEY,
  patient_id      VARCHAR(60),
  actor_id        VARCHAR(60),       -- user who triggered action
  action          VARCHAR(100),      -- 'dispatch_sent' | 'patient_accessed' | etc.
  resource_id     VARCHAR(100),      -- dispatch_id | patient_id | etc.
  timestamp       TIMESTAMPTZ DEFAULT NOW(),
  details         JSONB,             -- context (success/failure reason, etc.)
  hash_chain      TEXT,              -- SHA-256(hash(prev) + this_event), tamper-detection
  signature       TEXT,              -- (future) RSA signature for non-repudiation
  
  -- Immutable: no UPDATEs or DELETEs allowed (enforced by trigger)
  INDEX idx_audit_log_timestamp (created_at DESC),
  INDEX idx_audit_log_patient (patient_id, timestamp DESC)
);
```

**Hash-chain verification endpoint:**
```javascript
GET /audit/verify

// Walks entire audit_log, verifies hash(event_i) = prev_hash
// Returns: { verified: true, last_verified_at: timestamp } or
//          { verified: false, broken_at: event_id, detail: "hash mismatch at event 12345" }
```

## Migrations

**Numbered 001 through 022+. Run at container startup via `src/migrations/runner.js`.**

**Runner logic:**
```javascript
1. Check schema_migrations table (created if not exists).
2. For each migration file:
   - Check if filename in schema_migrations.
   - If not, execute SQL file.
   - Insert filename into schema_migrations.
3. Log migration results.
```

**Safe to run repeatedly.** Migration runner is idempotent.

**Key migrations:**
- `001–011`: Core schema (dispatches, patients, hospitals, vitals, audit).
- `012–016`: Auth model, staff credentials, hospital workflows.
- `017–019`: Patient PIN, PHR scans, compliance fields.
- `020`: Synthetic monitor seed data.
- `021`: ATNA hash-chain trigger + indexes.
- `022`: Hash-chain backfill for existing audit rows.

## Connection Pool

**Postgres: 10 connections by default (configurable via `POSTGRES_POOL_SIZE`).**

**Behavior:**
- If all connections in use, subsequent requests queue (timeout 30s default).
- If queue full or times out, request fails with 503.

**Monitored:** Gauge `postgres_connection_pool` tracks connection count. Alert if > 8.

## Query Performance

**Slow queries (> 1s) logged via PostgreSQL `log_statement = 'mod'` setting.**

**Common slow queries to watch:**
```sql
-- Vitals aggregation
SELECT AVG(value), STDDEV(value) FROM vitals 
WHERE patient_id = $1 AND vital_type = $2 AND recorded_at > NOW() - INTERVAL '7 days'
  ORDER BY recorded_at DESC LIMIT 10000;

-- Dispatch history (hospital intake)
SELECT * FROM dispatches 
WHERE hospital_id = $1 ORDER BY created_at DESC LIMIT 100;

-- Audit log verification (walks entire chain on verify endpoint)
SELECT * FROM audit_log ORDER BY id ASC;
```

**Indexes prevent full table scans. Query planner should use indexes for all critical paths.**

## Backup Strategy

**pg_dump → gzip → S3 (cross-region replication).**

**Frequency:** Hourly (automated).

**Retention:** 30 days (delete old backups).

**RTO (Recovery Time Objective):** ~3 seconds (verified by drill 2026-06-14).

**RPO (Recovery Point Objective):** 1 hour (hourly backups).

## 12-Factor Compliance

- ✅ Database URL in env var (`DATABASE_URL`).
- ✅ Migrations run at startup (no manual steps).
- ✅ Connection pool configurable.
- ✅ Data persisted externally (not in code or containers).

See [Operations](./05-operations.md) for migration procedures.

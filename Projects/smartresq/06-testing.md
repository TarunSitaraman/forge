# SmartResQ Testing Strategy

## Test Types & Coverage

| Type | Location | Command | Target | Status |
|------|----------|---------|--------|--------|
| **Unit** | `src/tests/vitals.test.js` | `npm run test:unit` | Vitals validation, deduplication | ~40 tests, 70% coverage |
| **Integration** | `src/tests/integration.test.js` | `npm run test:integration` | Ingestor → Postgres → Redis | ~20 tests, 60% coverage |
| **Pipeline** | `src/tests/pipeline.integration.test.js` | `npm run test:pipeline` | SOS → dispatch (end-to-end) | ~15 tests, 45s timeout, verifies <5s SLA |
| **Services** | `src/tests/services.integration.test.js` | `npm run test:services` | Hospital, ambulance, auth integrations | ~30 tests, 30s timeout |
| **Dispatcher** | `src/tests/test_*.js` | `npm run test:dispatcher` | Routing logic, retry, SOS cancel | ~8 files, routing verified |
| **Load** | `src/tests/load/k6-script.js` | Manual (k6 load test) | 1000 concurrent patients, 60s | Not run in CI, required before hospital go-live |

**Overall coverage:** 60.8% line coverage (gate enforces ≥60%).

**Ratchet enforcement:** Coverage can only go up, never down. CI fails if coverage drops.

## Unit Tests

**Focus:** Vitals validation logic, deduplication, encryption/decryption.

```javascript
test('valid vital passes schema check', () => {
  const vital = { patientId: 'p1', HR: 80, SpO2: 98, timestamp: new Date() };
  expect(validateVital(vital)).toBe(true);
});

test('duplicate vital within 5 min is deduplicated', async () => {
  const vital = { patientId: 'p1', HR: 80 };
  await ingestor.ingestVital(vital);
  const result = await ingestor.ingestVital(vital);
  expect(result.deduplicated).toBe(true);
});
```

**Coverage:** Positive cases (valid inputs), negative cases (invalid inputs), edge cases (boundary values, null checks).

## Integration Tests

**Focus:** Ingestor → Postgres → Redis flow. Mocked hospital/ambulance.

```javascript
test('ingestor stores vital in postgres and publishes to redis', async () => {
  const vital = { patientId: 'p1', HR: 80 };
  await ingestor.POST('/vitals', vital);
  
  const row = await postgres.query('SELECT * FROM vitals WHERE patient_id = $1', ['p1']);
  expect(row).toBeDefined();
  
  const published = await redis.getPubSubMessages('vitals-raw');
  expect(published).toContainEqual(expect.objectContaining({ HR: 80 }));
});
```

**Scope:** Real Postgres + Redis (via docker compose), mocked external services.

## Pipeline Integration Tests

**Focus:** End-to-end SOS → dispatch verification. Real docker compose stack required.

```javascript
test('SOS triggers dispatch in <5s', async () => {
  const start = Date.now();
  const dispatchId = await triggerSOS('patient-001');
  const latency = Date.now() - start;
  
  expect(latency).toBeLessThan(5000);
  expect(dispatchId).toBeDefined();
  
  const dispatch = await postgres.query('SELECT * FROM dispatches WHERE dispatch_id = $1', [dispatchId]);
  expect(dispatch.status).toBe('dispatched');
});
```

**Timeout:** 45 seconds (allows time for docker compose stack to process).

**SLA verification:** Measures actual latency, asserts <5s.

## Services Integration Tests

**Focus:** Hospital, ambulance, auth integrations. Real database, 30s timeout.

```javascript
test('hospital receives dispatch notification via SSE', async () => {
  const sse = await connectToSSE('/hospital/hospital-001/incoming');
  const dispatchId = await triggerSOS('patient-001');
  
  const event = await waitForSSEEvent(sse, 'dispatch', { timeout: 3000 });
  expect(event.data).toContainEqual(expect.objectContaining({ dispatchId }));
});
```

**Coverage:** Real-time notifications, state transitions, authorization checks.

## Dispatcher Tests

**Focus:** Retry logic, routing decisions, SOS cancellation.

**Files:** `src/tests/test_retry_logic.js`, `test_routing.js`, `test_sos_cancel.js`, etc.

```javascript
test('failed dispatch retried with exponential backoff', async () => {
  // Mock orchestrator to fail first 2 times
  let attempts = 0;
  mockOrchestrator.POST('/dispatch/decide', () => {
    attempts++;
    if (attempts < 3) throw new Error('Network error');
    return { dispatchId: 'd1' };
  });
  
  const alert = { alertId: 'a1', patientId: 'p1' };
  const result = await dispatcher.enqueueDispatch(alert);
  
  expect(result.status).toBe('dispatched');
  expect(attempts).toBe(3); // succeeded on 3rd try
});
```

## Load Testing (k6 Script)

**Not run in CI. Required before hospital go-live.**

```javascript
// k6 test: 1000 concurrent patients, trigger SOS, verify latency
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  vus: 1000, // 1000 virtual users
  duration: '60s',
};

export default function () {
  const url = 'http://localhost:3000/alerts/sos';
  const payload = {
    patientId: `patient-${__VU}`,
    lat: 13.0067,
    lng: 80.2206,
  };
  const res = http.post(url, JSON.stringify(payload), { headers: { 'Content-Type': 'application/json' } });
  check(res, {
    'status is 202': (r) => r.status === 202,
    'latency < 5s': (r) => r.timings.duration < 5000,
  });
  sleep(1);
}
```

**Targets:**
- Dispatch latency p99 < 3s (not < 5s under load).
- CPU < 70%.
- Memory < 1GB.
- Queue empty after spike (no backlog).

**Run:** `k6 run src/tests/load/k6-script.js`.

## Manual Testing (QA Checklist)

**No Selenium/Playwright automation yet (in TODO). Manual QA required:**

1. **Patient App Flow**
   - Register → create account.
   - Pair wearable device (Fitbit, Apple Watch, etc.).
   - Trigger SOS.
   - See real-time status updates (ambulance en route, hospital ready, handover complete).

2. **Hospital App Flow**
   - See incoming dispatch in queue (SSE real-time update).
   - Accept patient.
   - Admit to bed.
   - Record handover.

3. **Ambulance App Flow**
   - See dispatch notification.
   - Accept dispatch.
   - Send GPS updates every 30s.
   - See ETA countdown.
   - Mark arrived at scene.
   - Mark handover complete at hospital.

4. **Paramedic Watch Flow**
   - Compact display of active dispatch (patient name, vitals, ETA).
   - No direct actions (read-only).

5. **Monitoring (Real-Time)**
   - Prometheus (:9090) shows metrics (dispatch latency, queue depth).
   - Grafana (:9003) shows dashboards updating live.
   - Synthetic monitor probe every 5 min (check latency, steps completed).

## Coverage Gaps (Path to 75%)

**Current: 60.8%. Target: 75%.**

**Missing coverage:**
- Route handlers (hospital, ambulance, patient UIs) — need supertest.
- Race-condition scenarios (concurrent SOS on same hospital) — need `FOR UPDATE SKIP LOCKED` tests.
- Dispatch state machine transitions (all state paths).

**Effort:** ~200–400 lines of test code. Timeline: 1 sprint.

## Test Execution

**Local:**
```bash
npm run test:unit
npm run test:integration
npm run test:pipeline  # requires docker compose
npm run test:services  # requires docker compose
npm run test:dispatcher
```

**CI (on every push to main):**
```bash
npm run test:unit
npm run test:integration
npm run test:services
# (pipeline tests run on merge, slower)
```

**Before hospital go-live:**
```bash
npm run test:*
k6 run src/tests/load/k6-script.js  # manual, 60s
# Verify load test results: p99 < 3s, CPU < 70%, memory < 1GB
```

## Mocking Strategy

**External services mocked in tests:**
- Hospital EHR API (FHIR endpoint).
- Ambulance CAD system (EMRI).
- SMS/WhatsApp providers.

**Why:** Allows testing without real hospital API credentials or service dependencies.

**Library:** `nock` (HTTP mocking), `redis-mock` (Redis client mocking).

## Test Data

**Seed data in `src/migrations/020_synthetic_monitor.sql`:**
- 4 test patients (with different profiles).
- 4 test hospitals (SRM, Apollo, MIOT, Fortis).
- 4 test ambulances.
- 2 test staff accounts (doctor, nurse).

**Reset between tests:** `npm run test:reset` clears all data, re-runs migrations.

## CI/CD Test Gating

**PR cannot merge unless all tests pass:**
- Unit tests (5 sec).
- Integration tests (10 sec).
- Services tests (30 sec).
- Coverage check (60% minimum).

**Time to merge:** ~45 sec (parallelized).

**Flaky tests:** If a test fails intermittently, tagged with `@flaky` comment. Monitored for reliability issues. Retry up to 3 times in CI.

# SmartResQ Security Posture

## Threat Model

| Threat | Mitigation | Residual Risk |
|--------|-----------|----------------|
| **Vitals in transit (MitM)** | HTTPS only (TLS 1.3). Cloudflare SSL Full(Strict). HSTS preload. | Negligible (TLS 1.3 is standard). |
| **Vitals at rest** | AES-256-GCM encryption. Key in env var (not on disk). Rotated on deploy. | Acceptable (key management via env + secrets manager). |
| **Patient ID leakage** | JWT auth on every endpoint. RBAC (patient can only query own data). IDs masked in logs. | Acceptable (access control + audit trail). |
| **Dispatch manipulation** | Dispatcher validates alert schema. Orchestrator re-checks hospital capacity at decision time (not cached). Fallback if no hospital available. | Acceptable (dual validation). |
| **Hospital EHR compromise** | SmartResQ doesn't store EHR data. Only sends FHIR bundle (pre-arrival context). | Acceptable (SmartResQ not attack surface for EHR). |
| **Insider threat (admin reads all patient data)** | Audit log tamper-resistant (hash-chain). All admin access logged. | Mitigated (not eliminated). Next owner should add break-glass audit alerts. |
| **Accidental SQL injection** | Parameterized queries enforced. ESLint rule forbids string interpolation in DB. CI enforces. | Negligible (automated enforcement). |
| **Supply chain (npm packages)** | npm audit (critical) gated in CI. Dependabot auto-updates. SBOM (future). | Acceptable (automated scanning). |
| **Replay attacks** | JWT includes `iat` (issued-at) and `exp` (expiry). Replay within 1h window possible. | Acceptable for non-financial operations (HTTPS prevents interception). |

## Secrets Management

**Never in code or committed `.env` files.**

**Current:** Stored in `/opt/smartresq/.env.prod` on DigitalOcean droplet (encrypted at rest, SSH access restricted).

**Next owner should:** Migrate to AWS Secrets Manager or Azure KeyVault (abstraction layer in place).

**Required secrets:**
- `JWT_SECRET` (≥32 chars, validated at startup in prod).
- `CRYPTO_KEY` (AES-256 key for vitals encryption).
- `INTERNAL_SERVICE_SECRET` (inter-service auth).
- `POSTGRES_PASSWORD`.
- `REDIS_PASSWORD`.
- `GF_SECURITY_ADMIN_PASSWORD` (Grafana).

**Validation:** Startup guard rejects weak secrets in prod mode. Prevents accidental deployment of dev secrets.

## Encryption at Rest

**AES-256-GCM used for all PHI (vitals, medications, medical history).**

```javascript
const cipher = crypto.createCipheriv('aes-256-gcm', key, iv);
const encrypted = Buffer.concat([
  cipher.update(plaintext, 'utf8'),
  cipher.final(),
  cipher.getAuthTag()
]);
```

**Key:** 32-byte key from `CRYPTO_KEY` env var.

**Nonce:** Rotated per vital (random, prepended to ciphertext for decryption).

**Decryption:** Hospital integration, relevance engine, patient app can decrypt (authenticated).

**Compliance:** DPDP-ready (encryption mandatory for sensitive data).

## Role-Based Access Control (RBAC)

**Roles:** `doctor`, `nurse`, `admin`, `dispatcher`.

**Enforcement:** Every HTTP endpoint checks role via `requirePermission(role)` middleware.

**Example:**
- `POST /hospital/{id}/admit` → requires `doctor` or `nurse`.
- `GET /audit/dispatch-log` → requires `admin`.
- `POST /dispatch/decide` → requires `dispatcher` or `admin`.

**Scope:** Hospital staff can only view own hospital's dispatches. Patients can only view own records.

**Implementation:** JWT payload includes `role` and `hospital_id` (if applicable). Middleware verifies before handler runs.

## Audit Trail (ATNA)

**ATNA = Audit Trail and Node Authentication (HIPAA-equivalent for audit).**

**Immutable log:** INSERT-only Postgres table. No UPDATEs or DELETEs allowed (enforced by trigger).

**Hash-chain:** Each audit event includes hash of previous event (SHA-256). Creates tamper-evident chain.

```
Event 1: id=1, action=dispatch_sent, hash=hash(prev)
Event 2: id=2, action=hospital_acknowledged, hash=hash(Event1)
Event 3: id=3, action=ambulance_arrived, hash=hash(Event2)
```

**Verification:** Endpoint walks chain from first to last, verifies each hash. Reports first broken link if tamper detected.

**Events logged:**
- Patient SOS triggered.
- Dispatch decision made.
- Hospital notified (success/failure).
- Ambulance notified (success/failure).
- Patient data accessed (by whom, when).
- Admin actions (subscription changes, role changes).

**Compliance:** Satisfies HIPAA-equivalent audit requirements. Tamper-resistant via cryptography (not just policy).

## Rate Limiting

**Redis-backed, fail-closed.**

**Endpoints limited:**
- `POST /alerts/sos` → 15/hour per patient.
- `POST /vitals` → 1000/hour per patient.
- `POST /comms/{dispatch_id}` → 100/hour.

**Implementation:** Redis key = `ratelimit:{endpoint}:{user_id}` with 1-hour TTL. Increment on each request. If > limit, return 429 Too Many Requests.

**Fail-closed:** If Redis down, rate limiter uses in-process fallback (memory-based, per-instance). Degraded but still functional.

## Automated Security Gates (CI)

**Every commit triggers security gates. All must pass before merge.**

1. **gitleaks:** Scans commit for secrets (API keys, credentials, tokens). Zero tolerance.
2. **semgrep:** Static analysis (OWASP, Node.js rules). Checks for SQL injection, XSS, path traversal, etc. Configured to warn on medium, fail on high.
3. **npm audit:** Dependency vulnerability scanner. Fails on critical. Non-critical auto-updated by Dependabot.
4. **Trivy:** Filesystem + IaC scanner. Checks Docker images, Terraform, for vulnerabilities.
5. **ESLint:** Code style + security rules. Forbids SQL string interpolation, console.log in production, etc.

**Result:** No secrets committed, no known vulnerabilities in dependencies, no obvious code defects.

**Caveat:** Automated gates catch known issues. Real pen-test commissioned by risk team before handling real PHI (high liability).

## No SQL Injection

**Parameterized queries enforced.**

```javascript
// Good
const result = await db.query('SELECT * FROM patients WHERE id = $1', [patientId]);

// Bad (forbidden by linter)
const result = await db.query(`SELECT * FROM patients WHERE id = ${patientId}`);
```

**Linter:** ESLint rule `no-sql-string-interpolation` forbids second pattern. CI enforces.

**Semgrep:** Additionally scans for missed string interpolation.

**Result:** No SQL injection risk (barring new linter false negatives, caught in code review).

## Auth Token Security

**JWT-based. Signed with `JWT_SECRET`.**

**Payload:**
```json
{
  "sub": "user-123",
  "role": "nurse",
  "hospital_id": "hospital-001",
  "iat": 1719000000,
  "exp": 1719003600
}
```

**Lifetime:** 1 hour. Refresh token rotation (separate endpoint, requires current JWT + refresh token).

**Validation:** Every endpoint verifies signature + expiry. Cached locally (1h TTL) to avoid repeated lookups (staff-auth service).

**Short-lived:** Minimizes impact of token compromise (attacker's window = 1h max).

## Secrets Scanning

**Gitleaks scans every commit for patterns:**
- AWS keys (AKIA...).
- Private keys (-----BEGIN RSA PRIVATE KEY-----).
- Database URLs (postgres://user:pass@host).
- API keys, tokens, etc.

**Zero tolerance:** If pattern detected, commit fails. Developer must remove, force-push (with care).

**Bypass:** Gitleaks has whitelist for false positives (e.g., test/fixture data). Whitelist requires code review + approval.

## Data Residency

**CI job `check-india-residency.sh` verifies all infrastructure in India.**

**Checks:**
- AWS region (ap-south-1 only).
- Azure region (centralindia only).
- No egress to non-India endpoints.

**Purpose:** DPDP compliance (personal data must stay in India).

## Compliance Flags

**DPDP Consent:** `dpdp_consent_given` on patient record. Dispatch blocked if false.

**SaMD Classification:** Compliance service checks regulatory scope on every dispatch. Currently scoped as "emergency coordination," not "diagnosis." Prevents accidental mis-classification.

**Break-Glass Audit:** Not yet implemented. Next owner should add alerts for admin reads (potential insider threat).

See [Monitoring](./07-monitoring.md) for audit log monitoring setup.

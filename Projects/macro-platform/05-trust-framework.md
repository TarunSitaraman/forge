# Trust Framework (Nine Pillars)

## Mental Model

A production-grade AI governance layer, aligned to GDPR/SOX/MiFID II
patterns, that wraps the entire platform: 22 pillar-specific API
endpoints, 22 dedicated database tables, and a 35-test integration
suite, mounted via a single `TrustLayer.mount(app)` call (see
[Architecture](./02-architecture.md)). Each pillar answers a distinct
governance question — treat this doc as a lookup table for "which
pillar owns this concern" when extending the platform.

## Pillar 1 — Reliability

**Owns:** system availability, data freshness, LLM output confidence.

| Module | What It Does |
|---|---|
| `retry_policy.py` | `RetryPolicy` + `CircuitBreaker` for transient downstream failures |
| `health_check.py` | `/health` and deep `/health/deep` validation endpoints |
| `sla_monitor.py` | Data-freshness SLA tiers: Tier 1 (15 min), Tier 2 (2 hrs), Tier 3 (24 hrs), aligned to MiFID II |
| `extraction_thresholds.py` | LLM confidence decision engine: `AUTO_ACCEPT` (≥0.90), `QUEUE_REVIEW` (0.70-0.90), `REJECT` (<0.70) |

**Tables:** `sla_violations`, `extraction_decisions`.

## Pillar 2 — Security

**Owns:** endpoint protection, resource access, crawl-rate governance.

| Module | What It Does |
|---|---|
| `auth.py` | HMAC-SHA256 API key auth (bcrypt storage) + Azure AD RS256 JWT verification over live JWKS |
| `rate_limiter.py` | Token-bucket limiter, Postgres-backed, per-role limits (`PUBLIC`, `EXTERNAL_BUSINESS`, `INTERNAL_ANALYST`, `ADMIN`, `DATA_GOVERNANCE`) |
| `bot_detection.py` | Header-rotation detection + escalating cooldowns (1hr → 4hr → 24hr → permanent on 3rd strike) |
| `secret_manager.py` | Masks raw API secrets in outputs |

**Tables:** `api_keys`, `rate_limit_buckets`, `blocked_sources`.

**Note per the summary doc:** the JWT verification explicitly *"repaired
previous insecure verification bypasses"* — worth remembering that this
pillar has a documented history of a real vulnerability fix, not just
greenfield design; a good reminder to audit auth code changes carefully
in any project with this pattern.

## Pillar 3 — Safety

**Owns:** off-topic prevention, unauthorized financial-projection
blocking, output auditing.

| Module | What It Does |
|---|---|
| `guardrails.py` | `GuardrailEngine` — blocks out-of-scope prompts (curated macro-topic whitelist) and investment-advice requests; inserts disclaimers on forecasting language |
| `output_validator.py` | Inspects output length; cross-checks LLM statistics against Gold-layer facts |

**Tables:** `guardrail_audit_log`.

This is the pillar directly enforcing system-prompt rules 1 and 5 from
[Agent Framework](./03-agent-framework.md) at the framework level, not
just via prompt instruction.

## Pillar 4 — Privacy

**Owns:** PII sanitization, consent, erasure.

| Module | What It Does |
|---|---|
| `pii_scanner.py` | Regex-based stripping of credit cards, phone numbers, emails, SSNs from prompts |
| `consent_manager.py` | Logs compliance permissions; stores SHA-256 hashes of user IDs/IPs (never raw PII) |
| `data_retention.py` | Scheduled purge of logs beyond the compliance retention period |

**Tables:** `privacy_audit_log`, `user_consents`, `retention_audit_log`.

## Pillar 5 — Sustainability

**Owns:** cost/resource tracking, crawl efficiency.

| Module | What It Does |
|---|---|
| `cost_tracker.py` | Per-source operational spend: LLM tokens, storage GB, network requests |
| `crawl_optimizer.py` | Prevents duplicate ingestion via ETag headers + content-hash comparison |
| `resource_profiler.py` | Storage footprint across Bronze/Silver/Gold, measured every 6 hours |

**Tables:** `cost_tracking`, `crawl_opt_log`, `resource_metrics`.

## Pillar 6 — Explainability

**Owns:** extraction traces, score breakdowns.

| Module | What It Does |
|---|---|
| `source_selector.py` | Logs why a particular source was chosen when resolving conflicting data |
| `anomaly_explainer.py` | Plain-language explanations for out-of-range/conflict/revision anomalies |
| `llm_trace.py` | Tracks prompt, excerpt, extracted JSON, model version, tokens, latency per LLM action (SOX audit trail) |
| `quality_score_breakdown.py` | The DQ score formula (see [Data Pipeline](./04-data-pipeline.md)) |

**Tables:** `explainability_log`, `llm_extraction_traces`.

## Pillar 7 — Data Quality

**Owns:** feed validation, conflict resolution, revision history.
Detailed in [Data Pipeline](./04-data-pipeline.md) — summarized here:
four-stage validator (schema, numeric range, cross-indicator
consistency, freshness); conflict resolver (routine <1% vs. major >5%
variance); revision tracker (>10% flagged significant); weekly
per-source reputation scorecards.

**Tables:** `conflict_log`, `indicator_revisions`, `source_scorecards`.

## Pillar 8 — Transparency

**Owns:** citations, provenance.

| Module | What It Does |
|---|---|
| `chatbot_citations.py` | Re-prompts the LLM if it omits attribution; adds dynamic citation links to source records |
| `citation_trail.py` | Maps provenance across ingestion/cleaning/serving |
| `governance_artefacts.py` | Version-controlled governance policy documents |

**Tables:** `citation_trails`, `governance_policies`.

Cross-reference [Agent Framework](./03-agent-framework.md)'s citation
refactor (structured-metadata-based, not regex) — that implementation
detail is what this pillar's `chatbot_citations.py` builds on top of.

## Pillar 9 — Fairness & Accountability

**Owns:** geographic representation audits, reviewer task assignment,
regional bias flagging.

| Module | What It Does |
|---|---|
| `coverage_disclosure.py` | Warns when country-indicator coverage falls below 60% |
| `bias_monitor.py` | Monitors query diversity across sessions for regional bias |
| `human_oversight.py` | Routes ingested records: Auto-Approve (≥90), Review Queue (70-90), Auto-Reject (<90) |
| `accountability_chain.py` | 5-tier escalation chain with per-level SLA deadlines and automated escalation |

**Tables:** `bias_alerts`, `accountability_tasks`, `oversight_approvals`.

## Testing

`tests/test_trust_integration.py` — 35 integration test cases validating
end-to-end behavior of each pillar:

```pwsh
pytest tests/test_trust_integration.py -v
```

## Common Mistakes to Avoid When Extending This

- **Adding a new governance concern without checking if an existing
  pillar already owns it** — 9 pillars covering reliability, security,
  safety, privacy, sustainability, explainability, data quality,
  transparency, and fairness/accountability is already broad; a 10th
  pillar should be a deliberate decision, not a reflexive one (see the
  index's proportionality gut-check note).
- **Modifying a pillar's module without updating its corresponding
  integration test in `test_trust_integration.py`** — the 35-test suite
  is the mechanism that keeps 22 endpoints and 22 tables from silently
  drifting out of sync with each other.
- **Treating this framework as fully independent of the agent/data
  layers** — pillars 3, 4, and 8 specifically instrument the
  `AgentOrchestrator`'s output path; changes to the orchestrator's
  response shape can break guardrail/redaction/citation logic that
  expects a specific structure.

## Related Docs

[Agent Framework](./03-agent-framework.md) and
[Data Pipeline](./04-data-pipeline.md) for the two systems this
framework wraps and instruments.

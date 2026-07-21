# Data Pipeline (Medallion Architecture)

## Mental Model

Data moves through three progressively-trusted layers, never skipping
one: **Bronze** (raw, unvalidated) → **Silver** (cleaned, DQ-scored,
gated by a human review queue) → **Gold** (production-ready, embedded
for semantic search). The gate between Silver and Gold is the
platform's core trust mechanism — nothing reaches Gold, and therefore
nothing is retrievable by the chatbot, without clearing a data-quality
threshold or a human reviewer's sign-off.

## Verified Dagster Structure (from `src/orchestration/jobs.py`)

```python
all_assets = load_assets_from_modules([assets])

ingestion_job = define_asset_job(
    name="full_ingestion_job",
    selection=AssetSelection.groups("bronze", "silver", "gold", "news", "analytics"),
)

daily_ingestion_schedule = ScheduleDefinition(
    job=ingestion_job,
    cron_schedule="0 0 * * *",  # Daily at midnight
)
```

Five asset groups confirmed directly from source: `bronze`, `silver`,
`gold`, `news`, `analytics` — the pipeline runs as one daily-scheduled
job materializing all of them together, not as independently-scheduled
stages. This means a downstream Gold-layer failure is diagnosed by
walking back through the same run's Bronze/Silver asset materializations,
not by cross-referencing separate job schedules.

## Bronze Layer — Raw Ingestion

Sources: **World Bank, IMF, FRED** (structured APIs) plus **LLM-
augmented web crawlers** (`src/agents/crawler.py`, using Playwright for
browser automation) for data not available via clean API.

## Silver Layer — Cleaning + Data Quality Scoring

Records receive a **composite DQ score**. Per
`trustworthy_ai_framework_summary.md`'s explainability pillar, the
formula is:

```
Score = (Accuracy × 0.40) + (Completeness × 0.30) + (Timeliness × 0.20) + (Consistency × 0.10)
```

**Records scoring below 90% route to a Human-in-the-Loop Review Queue**
(Streamlit page `5_review_queue.py`) rather than being silently dropped
or silently promoted — this is the same
auto-approve/review/auto-reject three-tier pattern seen in
[QuickCover](../quickcover/04-ai-ml-models.md)'s fraud scoring, applied
here to data quality instead of fraud risk. Per the Trust Framework's
Fairness pillar, the exact thresholds are: Auto-Approve (≥90), Review
Queue (70-90), Auto-Reject (<70).

**Conflict resolution** (`trust/data_quality/conflict_resolver.py`):
when multiple sources disagree on a value, variances <1% are `ROUTINE`
(auto-select highest-reliability source), variances >5% are `MAJOR`
(routed to manual review) — a similar tiered-escalation pattern again.

**Revision tracking**: changes exceeding 10% are flagged as significant
revisions, logged for audit (relevant for indicators that get restated
after initial publication, common in macro data).

## Gold Layer — Production Data + Embeddings

Gold-layer records get **pgvector embeddings** (Jina AI or Gemini) for
semantic RAG retrieval — this is the layer the chatbot's tool registry
(`src/agents/tools/gold.py`) actually queries. Nothing in Bronze or
Silver is directly retrievable by the agent framework; promotion to Gold
is the trust boundary.

## Lineage Tracking

Full provenance from Bronze → Silver → Gold is tracked automatically
(`trust/transparency/citation_trail.py` maps provenance across all three
levels: ingestion, cleaning, serving). Combined with the
`src/agents/tools/lineage.py` tool, this means a chatbot answer's
citation can, in principle, be traced all the way back to which raw
Bronze record it originated from and what cleaning/scoring it underwent
— a materially stronger transparency guarantee than "here's a source
name," since it's traceable to a specific ingestion event.

## Forecasting (Prophet)

Automated time-series forecasting generates forward-looking estimates
on Gold-layer series; outlier detection flags records outside the 99%
confidence interval for anomaly review. This is what powers the Data
Explorer's "forecast toggle" and dashed-line forecast rendering (see
[Overview](./01-overview.md)).

## Common Mistakes to Avoid When Extending This

- **Adding a new data source directly into Silver or Gold**, skipping
  Bronze — breaks the raw-provenance guarantee the lineage system
  depends on; every source, however trusted, should land in Bronze
  first.
- **Manually promoting a Silver record to Gold outside the DQ scoring
  path** — bypasses both the score-based auto-approval logic and the
  human review queue, breaking the auditability the Fairness pillar's
  human-oversight escalation chain depends on.
- **Assuming the daily cron is the only ingestion trigger** — the
  crawler agent and news agent may have their own on-demand triggers
  distinct from the scheduled `full_ingestion_job`; confirm before
  assuming all data updates happen only at midnight.

## Related Docs

[Trust Framework](./05-trust-framework.md) for the Data Quality and
Fairness pillars this pipeline implements. [Agent Framework](./03-agent-framework.md)
for how agents query the Gold layer this pipeline produces.

# Overview

## What Is the Macro Intelligence Platform?

Per its own README: *"An agentic macroeconomic data intelligence
platform. Ingests, validates, forecasts, and serves macroeconomic
indicator data through a Medallion architecture. Features a
multi-tenant enterprise backend, a RAG-powered chatbot, and an
autonomous research agent for comprehensive reporting."*

Where [Personal Agent](../personal-agent/) is a single-user consumer
tool and [QuickCover](../quickcover/) is a hackathon consumer product,
this project is explicitly built and framed like an **enterprise data
platform** — multi-tenant from the ground up, with compliance framing
(GDPR/SOX/MiFID II) baked into the architecture rather than bolted on
later.

## Key Features

| Feature | What It Does |
|---|---|
| **Data Explorer** | High-performance filtering across 75,000+ records — 2025+ coverage, forecast toggles, SI unit formatting, dashed-line forecasting on charts |
| **Macro AI Chatbot** | Multi-turn conversation with citations, tool use, and safety guardrails against investment advice |
| **Summary Engine** | AI-generated country snapshots and indicator briefs with dynamic chart generation |
| **Autonomous Researcher** | Compiles professional deep-dive research reports (web search + internal data) into PDF format |
| **Anomaly Alerts** | Monitoring for critical macro signals (e.g., negative growth, hyperinflation) |

## Foundational Architecture Claims (from README)

- **Multi-Tenant Security Core** — JWT auth with RBAC (Admin, Analyst,
  Viewer roles); strict database-level row isolation via `tenant_id`
  across all data layers, chat histories, and audit logs.
- **Agent Orchestration Framework** — a custom `AgentOrchestrator` with
  tool-augmented, multi-step reasoning; a `ResponseVerifier` for
  grounding checks and citation enforcement to prevent hallucinations;
  a tool registry covering vector search, timeseries analysis, and
  cross-country comparisons.
- **DQ Trust & Lineage** — human-readable trust explanations for how
  data-quality scores are calculated; full provenance tracking from
  Bronze (raw) to Gold (production) via automated lineage.
- **Medallion Data Pipeline (Dagster)** — see
  [Data Pipeline](./04-data-pipeline.md) for the full breakdown.
- **Predictive Analytics (Prophet)** — automated time-series
  forecasting; outlier detection flags records outside the 99%
  confidence interval.

## Requirements

- Python 3.12+
- PostgreSQL 15+ with the `pgvector` extension
- API keys: Jina AI/Gemini (embeddings), Groq/Gemini/OpenRouter (LLMs),
  FRED (data source)

## Maturity Level

Active development, with a notably mature governance layer relative to
project age (createdAt 2026-03-22) — the nine-pillar Trust Framework
(see [Trust Framework](./05-trust-framework.md)) alone spans 22 API
endpoints, 22 database tables, and a 35-test integration suite. This
level of compliance-oriented infrastructure is unusual for a personal
project and is the platform's clearest differentiator from
[QuickCover](../quickcover/) or [Personal Agent](../personal-agent/),
both of which prioritize feature velocity over governance depth.

## Key Constraint

**Every LLM-generated claim must be grounded and cited; every
data-quality score must be explainable.** This isn't a nice-to-have
layered on top — the `ResponseVerifier`, the citation-enforcement
system, and the DQ scorecard/explainability modules are core to how the
platform is meant to be trusted for financial/economic decision-making
context, where an ungrounded hallucinated statistic is a much more
serious failure mode than in a casual chatbot.

## Next: Architecture

See [Architecture](./02-architecture.md) for the full Medallion pipeline
and multi-tenant security design.

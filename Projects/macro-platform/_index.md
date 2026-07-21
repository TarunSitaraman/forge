# Macro Intelligence Platform Knowledge Pack

*An agentic macroeconomic data intelligence platform — ingests, validates, forecasts, and serves macroeconomic indicator data through a Medallion architecture, with a multi-tenant enterprise backend, a RAG-powered chatbot, and a nine-pillar Trustworthy AI governance framework.*

**Status:** Active development. The most enterprise-grade of Tarun's personal projects by governance/compliance depth — explicitly aligned to GDPR, SOX, and MiFID II patterns despite being a personal project.

**Tags:** #status/active #type/project #stack/python #stack/fastapi #stack/dagster

**Key Constraint:** Every data point served must be traceable back to its source with a citation, and every LLM-generated claim must be grounded in retrieved data — the platform's core differentiator is *trust infrastructure*, not just data coverage.

## Quick Navigation

- **[Overview](./01-overview.md)** — What the platform does, key features, requirements
- **[Architecture](./02-architecture.md)** — Medallion pipeline, multi-tenant security core, tech stack
- **[Agent Framework](./03-agent-framework.md)** — The ReAct-style AgentOrchestrator, tool registry, response verification, the 7 specialized agents
- **[Data Pipeline](./04-data-pipeline.md)** — Dagster bronze/silver/gold assets, DQ scoring, human-in-the-loop review queue, pgvector embeddings
- **[Trust Framework](./05-trust-framework.md)** — The nine-pillar governance system (Reliability, Security, Safety, Privacy, Sustainability, Explainability, Data Quality, Transparency, Fairness/Accountability)
- **[API & UI](./06-api-and-ui.md)** — FastAPI routes, Streamlit multi-page frontend
- **[Deployment](./07-deployment.md)** — Local setup, Docker, environment variables, hosting
- **[Roadmap](./08-roadmap.md)** — Open questions and natural next steps

## Key Facts

| Aspect | Detail |
|--------|--------|
| **Repo** | [github.com/TarunSitaraman/macro-platform](https://github.com/TarunSitaraman/macro-platform) |
| **Language** | Python (FastAPI + Dagster + Streamlit), default branch `master` |
| **Data sources** | World Bank, IMF, FRED, plus LLM-augmented web crawlers |
| **Embeddings** | Jina AI / Gemini, stored via pgvector |
| **LLMs** | Groq / Gemini / OpenRouter (multi-provider, per README) |
| **Records** | 75,000+ indicator records (per README's Data Explorer feature description) |
| **Default admin login** | `admin@example.com` / `admin123` (local dev seed — see [Deployment](./07-deployment.md)) |

## Architecture At a Glance

```
Bronze (raw records: World Bank, IMF, FRED, web crawlers)
  ↓
Silver (cleaned + composite DQ score; <90% → Human-in-the-Loop Review Queue)
  ↓
Gold (production data + pgvector embeddings for semantic RAG)
  ↓
FastAPI backend (multi-tenant, JWT/RBAC) ← AgentOrchestrator (ReAct loop, tool-augmented)
  ↓
Streamlit UI (9 pages: overview, static data, crawler, explorer, review queue, chatbot, summaries, anomalies, researcher)
```

**The distinguishing design choice:** almost every other personal project prioritizes shipping features; this one prioritizes **governance infrastructure around the features** — 22 trust-framework database tables and 22 dedicated API endpoints exist specifically to make the platform's own outputs auditable, explainable, and compliant, layered on top of (not instead of) the core data/agent functionality.

## For Future You (Resuming Work on This Project)

Start with [Roadmap](./08-roadmap.md). Given the scale of the trust framework relative to the core product, a useful early question when resuming work is: *is the governance layer proportionate to actual usage, or has it outpaced the product it's meant to govern?* — worth an honest gut-check before adding a 10th pillar.

---

**Source repo analyzed:** `TarunSitaraman/macro-platform` @ `master`, via `README.md`, `trustworthy_ai_framework_summary.md`, file tree, and direct reads of `src/agents/runtime/orchestrator.py` and `src/orchestration/jobs.py` (2026-07-21).

---

## Related Systems / Reference

**If you're working on similar architecture:**
- [`Technologies/Docs/ai-agents.md`](../../Technologies/Docs/ai-agents.md) — the AgentOrchestrator here is a textbook ReAct implementation; a strong real-world companion to that doc's architecture section
- [`Technologies/Docs/rag.md`](../../Technologies/Docs/rag.md) — the Gold-layer pgvector embedding + citation-enforced chatbot is a concrete agentic-RAG implementation
- [`Technologies/Docs/vector-databases.md`](../../Technologies/Docs/vector-databases.md) — pgvector usage here, worth comparing against `personal-agent`'s pgvector usage for a second real-world reference point
- [`Projects/personal-agent/`](../personal-agent/) — sibling project; contrast personal-agent's single-tenant, cost-optimized LLM fallback against this platform's multi-tenant, compliance-optimized architecture

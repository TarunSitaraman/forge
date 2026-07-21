# API & UI

## FastAPI Backend (`src/api/`)

| Router | Purpose |
|---|---|
| `routes/auth.py` | Authentication (JWT issuance/verification) |
| `routes/data.py` | Indicator data query endpoints (backing the Data Explorer) |
| `routes/chat.py` | Chatbot endpoint — drives `AgentOrchestrator` (see [Agent Framework](./03-agent-framework.md)) |
| `routes/pipelines.py` | Pipeline status/trigger endpoints (Dagster integration) |
| `routes/review.py` | Human-in-the-loop review queue endpoints (Silver-layer DQ gate) |
| `routes/audit.py` | Audit log query endpoints |

Plus **22 trust-framework routers** mounted separately via
`TrustLayer.mount(app)` — see [Trust Framework](./05-trust-framework.md).
The core `routes/` above is the product surface; the trust routers are
the governance surface, mounted as a distinct layer rather than
interleaved.

## Streamlit Frontend (`src/ui/`)

Nine numbered pages (`_pages/1_overview.py` through `9_researcher.py`):

| # | Page | Purpose |
|---|---|---|
| 1 | Overview | Landing/summary view |
| 2 | Static Data | Reference/static indicator browsing |
| 3 | Crawler | Web-crawler ingestion monitoring/control |
| 4 | Explorer | The Data Explorer feature (75,000+ records, forecast toggles) |
| 5 | Review Queue | Human-in-the-loop DQ review (Silver → Gold gate) |
| 6 | Chatbot | The RAG-powered conversational interface |
| 7 | Summaries | AI-generated country snapshots/indicator briefs |
| 8 | Anomalies | Anomaly alert monitoring |
| 9 | Researcher | Autonomous deep-dive PDF report generation |

**The page numbering mirrors the data's own lifecycle** — pages 1-4 are
largely read/explore surfaces over Gold data, page 5 is the DQ
gatekeeping surface, pages 6-9 are agent-driven synthesis surfaces built
on top of the trusted Gold layer. This ordering is a reasonable mental
model for understanding the platform even before opening any specific
page.

## Static Assets

`src/ui/static/` — `app.js`, `index.html`, `style.css` — supplementary
static frontend assets alongside the Streamlit pages, purpose not fully
confirmed from file listing alone (possibly an embeddable widget or a
lighter-weight public-facing view distinct from the full Streamlit app;
verify directly before assuming).

## Common Mistakes to Avoid

- **Adding a new Streamlit page without considering where it sits in
  the lifecycle ordering above** — a new agent-driven feature probably
  belongs after page 9, not inserted mid-sequence, to preserve the
  read-then-govern-then-synthesize mental model the numbering implies.
- **Calling a trust-framework endpoint directly from a Streamlit page**
  instead of going through the core `src/api/routes/` layer that's
  meant to be the product-facing surface — couples UI code to
  governance internals that may change independently.

## Related Docs

[Trust Framework](./05-trust-framework.md) for the 22 additional
governance routers mounted alongside this core API surface.

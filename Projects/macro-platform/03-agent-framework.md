# Agent Framework

## Mental Model

The `AgentOrchestrator` (`src/agents/runtime/orchestrator.py`) is a
**bounded ReAct loop**: LLM reasons → optionally calls a tool → observes
the result → repeats, up to `max_steps` (default 5), then must answer.
This is a textbook ReAct implementation — worth cross-referencing
against [`Technologies/Docs/ai-agents.md`](../../Technologies/Docs/ai-agents.md)'s
ReAct section for the general pattern this concretely implements.

## Verified System Prompt (from direct source read)

```
You are a macroeconomic data analyst for the Macro Intelligence Platform.

You have access to tools that query verified gold-layer macro data, forecasts, and news.
Use tools when you need specific data. Call multiple tools if needed to answer thoroughly.

RULES:
1. ONLY discuss macroeconomic topics.
2. CITE every numeric claim as [Source: <source_name>, <period>].
3. Be concise and factual. Do not speculate beyond retrieved data.
4. If data is insufficient after using tools, say so clearly.
5. Never give personalized investment advice.
6. When you have enough data, respond directly without calling more tools.
```

**Rules 2 and 5 are the two that matter most for this platform's trust
model:** mandatory per-claim citation and a hard ban on personalized
investment advice. Both are enforced again at the framework level (see
[Trust Framework](./05-trust-framework.md)'s Safety and Transparency
pillars) rather than relying on the prompt alone — a defense-in-depth
pattern worth noting: the prompt instructs the behavior, and a separate
`GuardrailEngine`/`OutputValidator`/citation-builder layer verifies it
actually happened.

## Orchestrator Constructor (Verified)

```python
class AgentOrchestrator:
    def __init__(
        self,
        db: Session,
        tenant_id: UUID,
        registry: ToolRegistry,
        *,
        user_id: Optional[UUID] = None,
        session_id: Optional[UUID] = None,
        agent_name: str = "ChatbotAgent",
        max_steps: int = 5,
    ) -> None:
```

Note `tenant_id` is a required constructor argument, not optional —
every agent run is tenant-scoped from instantiation, reinforcing the
row-level isolation guarantee described in
[Architecture](./02-architecture.md). A `ResponseVerifier` and
`AgentTracer` are wired in automatically, not opt-in — every run is
verified and traced by construction.

## Runtime Components (`src/agents/runtime/`)

| Module | Role |
|---|---|
| `orchestrator.py` | The ReAct loop itself |
| `registry.py` | `ToolRegistry` — tool lookup/dispatch |
| `verifier.py` | `ResponseVerifier` — grounding checks, citation enforcement |
| `tracer.py` | `AgentTracer` — per-run tracing, persisted for audit |
| `citations.py` | Builds structured citations from `ToolResult` metadata directly (not regex-parsed from LLM text — see below) |
| `types.py` | `AgentRunResult`, `AgentStep`, `ToolResult` shared types |

## Citation Refinement — A Genuinely Good Reliability Pattern

Per `trustworthy_ai_framework_summary.md`, citations were refactored to
be **compiled directly from structured `ToolResult` metadata** returned
during tool execution, rather than regex-parsed from the LLM's free-text
response. Two concrete benefits:

1. **Eliminates formatting drift** — no failure mode where the model
   omits or malforms a `[Source: ...]` tag and the citation silently
   disappears.
2. **Guarantees attribution** — the citation list is a reliable record
   of what was *actually retrieved*, not what the model *claims* it used.

**This is a broadly reusable lesson:** whenever an LLM is expected to
emit structured metadata (citations, IDs, confidence scores) that the
system depends on programmatically, prefer deriving that metadata from
the tool-call layer (which you control) over parsing it out of the
model's free-text output (which you don't).

## The Specialized Agents (`src/agents/`)

| Agent | File | Role |
|---|---|---|
| Chatbot | `chatbot.py` | Multi-turn conversational interface, drives the citation refinement above |
| Q&A | `qa.py` | Direct question answering |
| Forecaster | `forecaster.py` | Prophet-based time-series forecasting |
| Researcher | `researcher.py` | Autonomous deep-dive report compilation (web + internal data → PDF) |
| Summarizer | `summarizer.py` | Country snapshots, indicator briefs |
| News | `news.py` | News ingestion/synthesis |
| Crawler | `crawler.py` | LLM-augmented web crawling for Bronze-layer ingestion |
| Alerts | `alerts.py` | Anomaly monitoring (negative growth, hyperinflation, etc.) |

## Tool Registry (`src/agents/tools/`)

| Tool file | Purpose |
|---|---|
| `chat_registry.py` | Tool registration/dispatch for chatbot |
| `forecast.py` | Forecasting tool exposed to agents |
| `gold.py` | Gold-layer data query tool |
| `lineage.py` | Data provenance/lineage query tool |
| `news.py` | News query tool |

## Common Mistakes to Avoid When Extending This

- **Instantiating an LLM call outside `AgentOrchestrator`** for a new
  feature — bypasses tracing, verification, and citation enforcement
  that exist specifically to keep every agent output auditable.
- **Adding a new tool without registering it in the `ToolRegistry`** —
  the orchestrator can only call tools it knows about; an unregistered
  tool function is dead code from the agent's perspective.
- **Increasing `max_steps` casually** — this bounds both cost and
  latency per query; raising it without a specific reason to allow
  longer reasoning chains widens the blast radius of a confused agent
  looping (see the general agent-reliability guidance in
  [`Technologies/Docs/ai-agents.md`](../../Technologies/Docs/ai-agents.md)).

## Related Docs

[Trust Framework](./05-trust-framework.md) for the Safety/Transparency
pillars enforcing this agent's output guarantees at the framework
level, beyond what the system prompt alone provides.

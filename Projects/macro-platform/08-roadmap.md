# Roadmap

## Confirmed Facts vs. Areas Needing Direct Verification

This pack was built primarily from the README and the trust-framework
summary doc, with two source files (`orchestrator.py`, `jobs.py`) read
directly to verify the most architecturally central claims. The
following weren't independently confirmed and are worth checking before
relying on them in a debugging or extension session:

- **Exact schema of core (non-trust) tables** — indicator/country/
  source tables backing the Data Explorer weren't directly inspected;
  `migrations/init.sql` is the source of truth.
- **`src/ui/static/` purpose** — noted as unconfirmed in
  [API & UI](./06-api-and-ui.md); could be a lightweight public view, an
  embeddable widget, or dead scaffolding.
- **Whether all `.env` variables are captured** — the README's snippet
  may not be exhaustive; check `.env.example` directly.
- **Whether the trust framework's 22 endpoints/tables count is still
  accurate** — this was stated in the summary doc as of its writing;
  verify against current `trust/` module count if the framework has
  grown since.

## Natural Next Steps (Inferred, Not Prescribed)

*(Reasonable directions given the current state — not a committed plan.)*

- **Proportionality review of the Trust Framework** — as flagged in the
  pack's `_index.md`, nine governance pillars is a substantial
  investment for a platform this early in its lifecycle (created
  2026-03-22). Worth an honest check on whether pillar depth matches
  actual usage volume, or whether effort would be better spent on core
  product surface (more data sources, better forecasting accuracy)
  before further governance expansion.
- **Eval harness maturity** — `eval/` contains `generate_ground_truth.py`,
  `score_rag.py`, and a `ground_truth.json`, suggesting a RAG evaluation
  harness already exists; confirm current coverage and consider
  expanding ground-truth cases as new data sources or agent capabilities
  are added (see [`Technologies/Docs/rag.md`](../../Technologies/Docs/rag.md)'s
  guidance on building a real labeled eval set).
- **Multi-provider LLM fallback** — the README lists Groq/Gemini/
  OpenRouter as available LLM providers, but whether the agent
  framework actually implements a fallback chain across them (as
  [Personal Agent](../personal-agent/03-llm-brain.md) does) or uses a
  single configured provider wasn't confirmed — worth checking
  `src/agents/llm_client.py` directly if reliability across providers
  becomes a concern.

## Project Applications / Why This Matters Beyond Itself

- **ReAct agent implementation reference** — the `AgentOrchestrator` is
  a clean, directly-readable implementation of the pattern documented
  generally in [`Technologies/Docs/ai-agents.md`](../../Technologies/Docs/ai-agents.md);
  worth returning to as a concrete example when implementing a new
  agent elsewhere.
- **Citation-from-structured-metadata pattern** — the specific lesson
  from [Agent Framework](./03-agent-framework.md) (derive citations from
  tool-call metadata, not regex-parsed LLM text) generalizes to any
  system where an LLM's free-text output needs to carry reliable
  structured data.
- **Tiered auto-approve/review/reject pattern, applied twice** — the
  same three-tier escalation structure appears in both the Data Quality
  pillar (record promotion) and the Fairness pillar (human oversight of
  ingestion) here, and independently in
  [QuickCover](../quickcover/04-ai-ml-models.md)'s fraud scoring. Worth
  recognizing as a general-purpose pattern for "automate the confident
  cases, route the ambiguous ones to a human, reject the confident-bad
  cases" — applicable well beyond either of these two specific domains.

## Retrospective

*(Fill in once there's more usage history — which pillars actually
caught a real issue vs. which have never fired in practice, whether the
Dagster daily-cron cadence has been sufficient, how the eval harness
scores have trended as data sources were added.)*

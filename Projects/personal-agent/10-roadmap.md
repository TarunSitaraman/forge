# Roadmap

## Evolution History (Reconstructed from CLAUDE.md vs. README)

The project has already gone through at least one major architectural
revision since its original plan. Worth understanding this history
before making further changes, since it explains several
otherwise-puzzling design choices found elsewhere in this pack:

| Aspect | Original Plan (`CLAUDE.md`) | Current State (`README.md`) |
|---|---|---|
| **Hosting** | Render (always-on server — explicitly required for webhooks + cron) | Vercel (serverless) + external cron-job.org |
| **LLM** | Gemini 2.0 Flash only (single provider) | 3-provider fallback: Groq → OpenRouter → Gemini |
| **Scheduling** | In-process `node-cron`, 2 briefs (10am, 7pm) | External HTTP-triggered cron, 7 distinct scheduled jobs |
| **Mode hours** | smartresq 8pm–12am, personal 12am–10am | smartresq 6pm–11pm, personal 11pm–10am |
| **Memory** | Todos/notes/learnings/conversation history only | Adds knowledge, events, skills, goals, user_insights, reminders, entity graph, spaced repetition |

**Implication:** `CLAUDE.md` is stale documentation, not a wrong
description — it accurately captures Phase 1 intent, but hasn't been
updated as the project moved through what looks like at least 2-3
additional phases of scope growth. If continuing work on this project,
**trust the README and the actual source over CLAUDE.md** for anything
where they conflict, and consider refreshing `CLAUDE.md` itself once a
clear evolution boundary is reached (matching the discipline this Forge
repo's own `CLAUDE.md` is meant to maintain — see
[`CLAUDE.md`](../../CLAUDE.md) at the Forge root for the pattern).

## Known Gaps (Confirmed)

- **Vision and Whisper integrations are stubs** — `src/integrations/
  vision.js` and `whisper.js` exist but the README explicitly states
  they're "not yet wired into the main flow," despite being imported in
  `queueProcessor.js`. Image and audio messages likely aren't fully
  handled end-to-end yet.

## Known Gaps (Unconfirmed — Verify Before Relying On)

These were surfaced during this pack's research but not fully traced
against the live code — flagged rather than asserted as fact, per this
project's own "don't guess, verify" documentation standard:

- **`pending_messages` / `dedup_messages` wiring:** schema exists with
  clear intent (retry + at-least-once dedup), but whether the live
  webhook path actually routes through this queue synchronously,
  asynchronously, or only partially wasn't confirmed. See
  [Reliability](./07-reliability.md).
- **`todos.embedding vector(1536)` dimension mismatch:** added via
  migration at 1536 dimensions, while `text-embedding-004` (used
  everywhere else in the codebase) outputs 768-dim vectors. If todos
  semantic search is actually live, this is either a bug or todos use a
  different (unconfirmed) embedding source. See
  [Memory System](./04-memory-system.md).
- **In-memory `modelFailCache` behavior under Vercel's serverless
  execution model:** cooldown tracking may not persist as expected
  between cold-started function invocations. See
  [Reliability](./07-reliability.md).
- **Fallback-chain worst-case timeout vs. Vercel's 60s function budget:**
  a full cascading failure through all 3 providers' individual timeouts
  could in theory exceed the platform ceiling. See
  [Reliability](./07-reliability.md).

## Natural Next Steps (Inferred, Not Prescribed)

*(These are reasonable directions given the current gaps and schema
intent — not a committed plan. Replace with actual intent once decided.)*

- Wire vision/whisper fully into the intent system so image/voice notes
  become first-class captures like text.
- Confirm and, if needed, fix the todos embedding dimension mismatch.
- Trace and document (or complete) the message queue's actual role in
  the live request path — this affects how confidently duplicate-
  message and transient-failure handling can be relied upon.
- Consider whether `CLAUDE.md` should be refreshed to match current
  architecture, the same correction this Forge repo's own README needed
  (see the quick-wins fix applied to Forge's README in a recent session
  — same category of drift, different repo).

## Project Applications / Why This Matters Beyond Itself

This project is a strong real-world reference for:
- **Multi-provider LLM fallback design** — see
  [`Technologies/Docs/llms.md`](../../Technologies/Docs/llms.md) for the
  general pattern; this project is a concrete, battle-tested
  implementation of it worth returning to as an example.
- **Prompt-as-product-design** — the system prompt in `brain.js` does
  real product work (persona, disambiguation guardrails, formatting
  constraints) beyond simple instruction-following; a good reference
  when writing prompts for [`Technologies/Docs/prompt-engineering.md`](../../Technologies/Docs/prompt-engineering.md)-
  adjacent work.
- **Free-tier-constrained reliability engineering** — a useful contrast
  case against [SmartResQ](../smartresq/)'s hard-SLA reliability story;
  worth comparing the two projects' `07-reliability.md`-equivalent docs
  side by side if writing about tradeoffs between cost-optimized and
  latency-optimized reliability design.

## Retrospective

*(Fill in once you have distance from active development — what worked,
what you'd redo, what surprised you about running an agent against real
daily use rather than a demo.)*

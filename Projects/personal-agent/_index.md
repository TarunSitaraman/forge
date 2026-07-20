# Personal Agent ("Blu") Knowledge Pack

*A WhatsApp-native personal AI agent and context-aware second brain — routes every message through a multi-tier LLM stack, stores memory in Postgres with vector search, and proactively surfaces what matters via scheduled briefs and nudges.*

**Status:** Deployed and live (Vercel serverless). Actively evolved since original Render/Gemini-only MVP — see [Roadmap](./10-roadmap.md) for the evolution history.

**Tags:** #status/active #type/project #stack/nodejs #stack/postgres #stack/vercel

**Key Constraint:** Zero-cost operation — every LLM provider, the database, hosting, and the external cron trigger are all free-tier. The three-round LLM fallback chain exists primarily to survive free-tier rate limits, not just for reliability.

## Quick Navigation

- **[Overview](./01-overview.md)** — What Blu is, who it's for, the three life-mode concept, maturity
- **[Architecture](./02-architecture.md)** — Message flow, mode detection, request lifecycle
- **[LLM Brain](./03-llm-brain.md)** — Multi-tier model routing, system prompt design, intent classification
- **[Memory System](./04-memory-system.md)** — Postgres schema, pgvector semantic search, spaced repetition, entity graph
- **[Integrations](./05-integrations.md)** — WhatsApp Cloud API, GitHub, Serper search, Expo push, vision/whisper stubs
- **[Proactive Features](./06-proactive-features.md)** — Scheduled briefs, nudges, the "Hermes layer" behavior design
- **[Reliability](./07-reliability.md)** — Model fallback/cooldown, message queue + dedup, known gaps
- **[Deployment](./08-deployment.md)** — Vercel serverless config, environment variables, external cron
- **[Mobile & Dashboard](./09-mobile-and-dashboard.md)** — Expo companion app, analytics dashboard
- **[Roadmap](./10-roadmap.md)** — Evolution history, known gaps, next steps

## Key Facts

| Aspect | Detail |
|--------|--------|
| **Repo** | [github.com/TarunSitaraman/personal-agent](https://github.com/TarunSitaraman/personal-agent) |
| **Live URL** | personal-agent-blond-beta.vercel.app |
| **Primary interface** | WhatsApp (Meta Cloud API) |
| **LLM stack** | Groq (primary) → OpenRouter (secondary) → Gemini (tertiary), 3-round fallback |
| **Database** | Neon serverless Postgres + pgvector |
| **Embeddings** | Gemini `text-embedding-004` (768-dim) |
| **Scheduling** | node-cron logic, triggered externally by cron-job.org (Vercel has no persistent cron on free tier) |
| **Companion apps** | Expo/React Native mobile app, Electron-style desktop app, web dashboard |

## Architecture At a Glance

```
WhatsApp (Meta Cloud API)
  ↓
Vercel Serverless Functions (api/)
  ↓
Intent Prefilter (regex, zero LLM cost) → LLM Brain (JSON-mode) if ambiguous
  ↓
Groq (3 models raced) → OpenRouter → Gemini   [3-round fallback chain]
  ↓
Action Executor → Neon Postgres (+ pgvector semantic search)
  ↓
Integrations (GitHub, Serper) + Scheduler (briefs/nudges via external cron)
```

**Core loop:** every message is classified into an intent (add/complete/list/search/system), executed against Postgres, and — critically — the response layer scans context after every action to proactively surface 1-2 related things Tarun should know, without being asked. This proactive-surfacing behavior (the "Hermes layer") is the project's actual differentiator over a plain chatbot-with-memory.

## For Future You (Resuming Work on This Project)

Start with [Roadmap](./10-roadmap.md) to see known gaps (vision/whisper are stubs, message queue/dedup tables exist in schema but their wiring wasn't fully traced during this pack's creation — verify before assuming they're live).

---

**Source repo analyzed:** `TarunSitaraman/personal-agent` @ `master`, via `README.md`, `CLAUDE.md`, `package.json`, `src/agent/brain.js`, `src/migrate_db.js`, `src/agent/queueProcessor.js` (2026-07-20).

---

## Related Systems / Reference

**If you're working on similar architecture:**
- [`Technologies/Docs/llms.md`](../../Technologies/Docs/llms.md) — LLM behavior fundamentals underlying the multi-tier routing here
- [`Technologies/Docs/ai-agents.md`](../../Technologies/Docs/ai-agents.md) — agent architecture patterns; compare Blu's single-agent tool-calling loop against the ReAct/multi-agent patterns documented there
- [`Technologies/Docs/prompt-engineering.md`](../../Technologies/Docs/prompt-engineering.md) — the system prompt in `src/agent/brain.js` is a strong real-world example of few-shot + strict-output-format prompting
- [`Technologies/Docs/vector-databases.md`](../../Technologies/Docs/vector-databases.md) — pgvector is used here rather than a dedicated vector DB; useful contrast
- [`Projects/smartresq/`](../smartresq/) — sibling project this agent's GitHub integration surfaces PR/issue status for

**If this project ever needs deep interview-style talking points:**
- Consider adding an `INTERVIEW_GUIDE.md` here (see `Projects/smartresq/INTERVIEW_GUIDE.md` for the format) once the project has more production mileage to draw concrete stories from.

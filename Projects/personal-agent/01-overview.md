# Personal Agent Overview

## What Is Blu?

Blu is a WhatsApp-native personal AI agent built for Tarun. Per its own
README, the explicit design intent is: *"It is not a todo app. It is a
context-aware second brain that knows which life mode he is in, routes
every message through a multi-tier LLM stack, stores memory in Postgres
with vector search, and proactively surfaces what matters via scheduled
briefs and nudges."*

**Tech Stack:** Node.js 20+ / Express 4, deployed as Vercel serverless
functions. Neon (serverless Postgres) + pgvector for memory. Meta
WhatsApp Cloud API for the primary interface.

**Who It's For:** A single user (Tarun), tuned specifically to his
schedule and context — not a general-purpose multi-tenant product.

## The Core Concept: Three Life Modes

Mode is detected automatically by time of day, injected into every LLM
prompt so Blu classifies and tags items without asking:

| Mode | Hours (per README) | Focus |
|------|-------|-------|
| `hexaware` | 10am – 6pm | Intern work, GenAI learning capture |
| `smartresq` | 6pm – 11pm | Startup tasks, PR reviews, product |
| `personal` | 11pm – 10am | Rest, general |

*(Note: `CLAUDE.md`, the earlier project-context file, lists slightly
different hours — 8pm–12am for smartresq, 12am–10am for personal — than
the current README. The README reflects the more recently updated
source of truth; `CLAUDE.md` appears to predate a mode-hours adjustment
and hasn't been kept in sync. See [Roadmap](./10-roadmap.md).)*

Mode can be overridden via message ("switch to personal mode") and
persists in the `state` table until midnight.

## Maturity Level

**Deployed and actively used** — this is not a prototype; `pushedAt`
timestamp and the depth of the schema (spaced repetition, entity graph,
prompt versioning, token tracking) indicate ongoing iteration well past
initial MVP.

**Architecture has already evolved once:** the original `CLAUDE.md`
plan specified Render (always-on server) + Gemini-only as the entire
stack, justified explicitly because "Vercel serverless won't work" for
webhooks + cron. The current README describes a Vercel serverless
deployment with a 3-provider LLM fallback chain and *external* cron
(cron-job.org hitting Vercel HTTP endpoints) — meaning the original
architectural objection to Vercel was resolved by moving scheduling
outside the platform rather than by keeping Render. This is worth
understanding if debugging scheduling reliability: crons are only as
reliable as the external cron-job.org service triggering them.

## Why This Project Exists (Design Rationale)

- **WhatsApp over Telegram/Slack/a custom app:** Tarun already lives in
  WhatsApp daily — meets the user where friction is lowest for capture.
- **Proactive over passive:** explicitly designed around the observation
  that Tarun is "organised but forgets to enter/check todos — needs
  proactive nudges, not passive lists" (from `CLAUDE.md`). This shapes
  nearly every downstream design decision — the scheduler, the
  "surface 1-2 related things after every action" behavior, and the
  stale-todo nudges all exist because a passive list wouldn't get
  checked.
- **Free-tier-everything:** every component (LLM providers, Neon,
  Vercel, cron-job.org, Expo push) is chosen to run at zero ongoing
  cost — this is a hard constraint that shapes the entire reliability
  story (see [Reliability](./07-reliability.md)).

## Key Constraint

**Zero-cost operation with acceptable reliability.** Unlike SmartResQ
(where the constraint is a hard <5s dispatch SLA), Blu's constraint is
softer but pervasive: every architectural choice — the 3-provider LLM
race with cooldown, the free external cron trigger, the free-tier
Postgres — trades some reliability/latency for running at $0/month. This
is the lens to view every other doc in this pack through.

## Next: Architecture

See [Architecture](./02-architecture.md) for the full request lifecycle
and mode-detection mechanics.

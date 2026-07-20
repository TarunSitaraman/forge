# Reliability

## Mental Model

Because every dependency here runs on a free tier, reliability isn't
"prevent failure" — it's **degrade gracefully through failure that free
tiers guarantee will happen** (rate limits, cold starts, an external
cron service occasionally missing a fire). Every mechanism in this doc
exists because some specific free-tier limitation was hit or anticipated.

## LLM Fallback and Cooldown

Covered in depth in [LLM Brain](./03-llm-brain.md) — summarized here
from a pure reliability lens: per-model (not per-provider) 5-minute
cooldown tracked in an in-memory `Map`. This is the primary defense
against Groq/OpenRouter free-tier rate limits taking the whole system
down — a single overloaded model gets skipped for 5 minutes while its
siblings and the other two providers continue serving requests.

**Reliability caveat:** `modelFailCache` is an in-memory `Map`, not
persisted to Postgres. On a serverless platform (Vercel), each function
invocation may get a cold instance — meaning the cooldown state doesn't
necessarily persist between invocations the way it would on an always-on
server (like the originally-planned Render deployment). Worth confirming
actual behavior under Vercel's execution model before relying on
cooldown state surviving between closely-spaced requests.

## Message Deduplication

`dedup_messages` (keyed on WhatsApp's `message_id`) exists specifically
because Meta's Cloud API webhook delivery is at-least-once, not
exactly-once — duplicate webhook fires for the same message are an
expected, not exceptional, occurrence. Any code path that processes an
inbound message should check this table before acting, to avoid
double-executing an action (e.g. adding the same todo twice) on a
redelivered webhook.

## Message Queue (`pending_messages`)

Tracks `status`, `attempts`, and `error_message` per inbound message,
plus token counts once processed. This is the infrastructure for retry-
with-backoff on transient failures (an LLM call timing out, a database
hiccup) without silently dropping the message. *(As noted in
[Memory System](./04-memory-system.md), whether this is fully wired into
the synchronous webhook path or represents partially-built
infrastructure wasn't confirmed during this pack's creation — read
`api/webhook.js` and `queueProcessor.js` directly before assuming
current queueing behavior.)*

## Timeout Budgets

From `brain.js`: Groq calls timeout at 25s, OpenRouter at 20s, Gemini at
20s (via `Promise.race` against a manual timeout). Vercel's function
`maxDuration` is set to 60s (`vercel.json`) — meaning the full 3-round
fallback chain, in the worst case (all three Groq models individually
timing out at 25s before falling through), could exceed the platform's
own function timeout. This is a real latent risk: a fully cascading
failure through all providers is bounded by the 60s platform limit, not
by the sum of each round's timeout, so a worst-case request could be
killed mid-fallback rather than completing with a degraded-but-successful
response from a later round.

## What Happens When Everything Fails

Not fully traced during this pack's creation — worth confirming: does a
complete LLM fallback exhaustion (all 3 providers, all models) result in
a graceful "I'm having trouble right now" WhatsApp reply, or a silent
failure / unhandled exception? Given the queue/dedup infrastructure
exists, a `status: 'failed'` write to `pending_messages` with the
`error_message` populated seems like the intended behavior — confirm
against actual code before relying on this in a debugging session.

## Common Mistakes to Avoid When Extending This

- **Assuming in-memory state (like `modelFailCache`) behaves the same
  on serverless as it would on an always-on server** — this is the
  single most likely source of "works locally, flaky in production"
  bugs in this codebase given the Render-to-Vercel migration.
- **Adding a new external API call without a timeout** — every existing
  LLM call has an explicit timeout; an unbounded call risks blowing the
  60s Vercel function budget on its own.
- **Processing a message without checking `dedup_messages` first** —
  reintroduces the duplicate-action bug this table was added to prevent.

## Related Docs

[Deployment](./08-deployment.md) for the Vercel/cron-job.org
infrastructure this reliability story depends on.
[LLM Brain](./03-llm-brain.md) for the fallback chain mechanics referenced
above.

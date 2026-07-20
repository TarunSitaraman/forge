# Deployment

## Topology

```
Meta WhatsApp Cloud API
       │ webhook POST
       ▼
Vercel Serverless Functions (api/*.js, each maxDuration: 60s)
       │
       ├── api/webhook.js    — WhatsApp inbound (GET verify + POST messages)
       ├── api/chat.js       — mobile app chat endpoint
       ├── api/todos.js      — todos REST API
       ├── api/health.js     — health check
       └── api/cron/*.js     — one file per scheduled job (see below)
       │
       ▼
Neon (serverless Postgres, +pgvector) ── always-on, separate from Vercel's cold-start model
```

**Every function-level file in `vercel.json` is capped at `maxDuration:
60`** — this is the platform ceiling every request (including the full
3-round LLM fallback chain) must complete within. See
[Reliability](./07-reliability.md) for why this is a real, not
theoretical, constraint on worst-case fallback latency.

## Why External Cron (cron-job.org), Not Vercel Cron

Vercel's own cron feature has constraints (and historically, paid-tier
gating) that don't fit this project's every-minute reminder job on a
free deployment. Instead, **cron-job.org** (a free external scheduler)
is configured to hit each `api/cron/*.js` endpoint on its own schedule
via plain HTTP GET/POST — meaning **scheduling reliability is entirely
outside Vercel's control**, dependent on a third-party free service
actually firing its configured triggers on time.

This directly explains why the original `CLAUDE.md` plan rejected
Vercel serverless in favor of Render (an always-on server with native
in-process `node-cron` scheduling) — that concern was real for the
original single-server design. The current architecture resolves it not
by proving Vercel-native cron works, but by moving scheduling
responsibility to an external always-on trigger source instead. Worth
remembering when debugging a missed brief or nudge: the first place to
check is whether cron-job.org actually fired, not just whether the
Vercel function itself errored.

## Cron Schedule Reference

| Endpoint | Schedule (IST) | Purpose |
|---|---|---|
| `api/cron/morning.js` | 9:00am Mon-Fri | Morning brief + stale todo alert |
| `api/cron/evening.js` | 6:00pm daily | Mode transition brief |
| `api/cron/nudge.js` | 9:00pm daily | Proactive nudge |
| `api/cron/goal.js` | 10:00pm daily | Goal follow-up |
| `api/cron/weekly.js` | Sun 8:00pm | Weekly review + conversation trim |
| `api/cron/pulse.js` | Sun 10:00am | Tech Twitter pulse |
| `api/cron/reminder.js` | Every minute | Reminder + event 15-min heads-up |

## Environment Variables

```env
WHATSAPP_TOKEN=          # Meta permanent access token
WHATSAPP_PHONE_ID=       # Phone number ID from Meta Developer Console
WHATSAPP_VERIFY_TOKEN=   # Any string (webhook verification handshake)
MY_WHATSAPP_NUMBER=      # Tarun's number, E.164 format (91XXXXXXXXXX)

GROQ_API_KEY=            # Primary LLM provider
OPENROUTER_API_KEY=      # Secondary LLM fallback
GEMINI_API_KEY=          # Tertiary LLM + embeddings

DATABASE_URL=            # Neon Postgres connection string (pgvector-enabled)
SERPER_API_KEY=          # Google search API
GITHUB_TOKEN=            # GitHub PAT for SmartResQ repo integration
GITHUB_REPO=             # e.g. TarunSitaraman/SmartResQ-dev

PORT=3000
TZ=Asia/Kolkata
APP_URL=                 # Public deployed URL (keep-alive ping target)
```

## Local Development

`npm run dev` runs `node --watch src/server.js` — a local Express server
for development, distinct from the Vercel serverless functions in
`api/` used in production. Confirm whether `src/server.js` fully mirrors
the `api/*.js` routing before assuming local dev behavior exactly
matches production request handling.

## Common Mistakes to Avoid

- **Assuming a missed scheduled brief is a code bug** — check
  cron-job.org's own dashboard/logs first, since the trigger source is
  external to Vercel entirely.
- **Adding a new long-running operation to any `api/` function without
  updating its `maxDuration` in `vercel.json`** — defaults may be lower
  than the 60s already configured for existing endpoints.
- **Testing only via `npm run dev`** — since production runs through
  `api/*.js` Vercel functions, not `src/server.js` directly, behavior
  can diverge (especially around request/response object shape, which
  differs between Express and Vercel's serverless function signature).

## Related Docs

[Reliability](./07-reliability.md) for the timeout-budget implications
of this deployment model.

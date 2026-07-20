# Proactive Features

## Mental Model

This is the project's actual thesis: **a passive list doesn't get
checked, so the agent must push, not just store.** Every feature here
exists to answer "what should Tarun know right now, without having to
ask?" — the scheduled briefs answer it on a timer, the nudges answer it
based on state (staleness, pending goals), and the per-response
"Hermes layer" answers it inline after every single action.

## Scheduled Briefs (via `src/scheduler/briefs.js`, triggered by external cron)

| Brief | Schedule (IST) | Content |
|---|---|---|
| **Morning Brief** | 9:00am Mon–Fri | Hexaware standup format: yesterday's wins/notes → today's tasks → mental blockers. Sent via WhatsApp + push. |
| **Mode Transition Brief** | 6:00pm daily | Marks end of intern day. Summarizes Hexaware wins, surfaces SmartResQ todos + open PRs (via the GitHub integration), asks for the "One Big Thing" goal via an interactive button. |
| **Weekly Review** | Sun 8:00pm | Completed vs. added todos, new learnings/notes, open items, honest progress assessment. Triggers conversation history trim to 200 messages afterward. |
| **Tech Pulse** | Sun 10:00am | Extracts Tarun's tech interests from the `knowledge` store, runs a live Serper search, synthesizes a curated digest — the one brief that's generative/exploratory rather than a summary of stored state. |

## Reactive Nudges

| Nudge | Trigger | Mechanism |
|---|---|---|
| **Stale todo alert** | 9am Mon–Fri, alongside morning brief | Flags todos untouched 5+ days, with snooze/dismiss buttons |
| **Proactive nudge** | 9pm daily | Priority-ordered: pending "One Big Thing" goal → automation suggestion from `user_insights` → stale blockers |
| **Goal follow-up** | 10pm daily | Checks if the daily goal is still pending, pings for progress |
| **Reminder system** | Every minute (cron) | Checks `remind_at` on todos, fires WhatsApp button messages (Done / Snooze 1hr) |
| **Event reminders** | Every minute (cron) | 15-minute heads-up before calendar events, via button messages |

**Note the every-minute cron for reminders/events** — this is the
tightest-scheduled job in the system, and its reliability depends
entirely on the external cron-job.org trigger firing consistently; see
[Reliability](./07-reliability.md) and [Deployment](./08-deployment.md)
for the implications of relying on a free external cron service for a
per-minute job.

## The "Hermes Layer" — Per-Response Proactive Surfacing

Distinct from the scheduled/triggered features above, this is inline
behavior baked directly into the system prompt (see
[LLM Brain](./03-llm-brain.md)): after executing **any** action, the
model is instructed to scan the context block and surface 1-2 related
things, with explicit trigger conditions:

- After completing a todo → mention other related pending tasks in the
  same context, if any exist
- If unreviewed learnings exceed 6 → append a nudge to review them
- If an upcoming event is within 2 hours → lead the reply with a
  heads-up
- If semantic memory search surfaces a relevant past connection →
  mention the single most useful one, in one line
- After adding a learning → mention a related note/knowledge fact if one
  exists

This is deliberately **prompt-level behavior, not code-level** — the
model decides what's worth surfacing based on instructions and the
context block it's given, rather than a hardcoded rule engine. The
tradeoff (discussed in [Architecture](./02-architecture.md)'s decision
table) is flexibility vs. a risk of the reply feeling noisy if the model
over-surfaces.

## Self-Learning: Behavioral Pattern Extraction

Every 20 messages, `analyzePatterns()` runs asynchronously: feeds the
last 40 conversation turns to the LLM, extracts 2-3 behavioral insights
(the README's example: *"Tarun tends to defer SmartResQ tasks past
11pm"*), and stores them in `user_insights`. These insights feed back
into the system prompt context on future calls — closing a feedback loop
where nudges become more personalized the longer the agent runs.

**Rolling context summary**, refreshed on the same 20-message cadence:
last 40 messages condensed into 3-5 sentences, stored with a 24-hour
TTL — this prevents prompt bloat from ever-growing conversation history
while preserving short-term continuity without needing to send the full
raw history on every call.

## Common Mistakes to Avoid When Extending This

- **Adding a new proactive trigger only in the system prompt without
  also feeding the relevant data into the context block** — the model
  can't surface what it was never shown, no matter how the prompt is
  worded.
- **Over-tuning nudge frequency without a feedback signal** — the
  `user_satisfaction` column added to `conversations` (see
  [Memory System](./04-memory-system.md)) is the natural place to close
  this loop, but confirm whether it's actually populated anywhere before
  assuming this feedback mechanism is live.

## Related Docs

[LLM Brain](./03-llm-brain.md) for the exact system prompt language
driving the Hermes layer. [Memory System](./04-memory-system.md) for the
`user_insights` and rolling-summary storage mechanics.

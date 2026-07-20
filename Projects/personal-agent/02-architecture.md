# Architecture

## Mental Model

Blu is a **classify-then-execute agent loop wrapped around a WhatsApp
webhook**. Every inbound message goes through: cheap regex prefilter →
(if ambiguous) LLM classification into a structured intent → action
execution against Postgres → a reply that proactively scans context for
anything else worth surfacing.

```
WhatsApp message arrives
  ↓
api/webhook.js (Vercel serverless entry point)
  ↓
Regex Prefilter (PREFILTER_RULES — zero LLM cost)
  │
  ├─ matches a common unambiguous command → skip LLM, execute directly
  │
  └─ ambiguous / complex → LLM Brain
        ↓
     Mode Detection (time-of-day, or DB override) injected into prompt
        ↓
     3-Round LLM Fallback (Groq → OpenRouter → Gemini) — JSON-mode output
        ↓
     Action Executor (single action, or "actions" array for compound requests)
        ↓
     Postgres (Neon) — todos / notes / learnings / events / knowledge / etc.
        ↓
     Proactive Scan — surface 1-2 related context items in the reply
        ↓
     WhatsApp reply (text, button, or list message)
```

## Why Prefilter Before LLM?

The regex prefilter (`PREFILTER_RULES` in `brain.js`) exists purely for
**cost and latency**: common commands like `todos`, `done: X`, `note: X`
are handled with zero LLM calls. Only genuinely ambiguous or
free-form messages reach the LLM. This is the same "cheap check before
expensive check" instinct as a cache-before-database pattern — applied
to LLM cost instead of database load.

## Mode Detection

Mode (`hexaware` / `smartresq` / `personal`) is computed from time of
day by default, but can be overridden by message and persists in the
`state` key-value table until midnight (`src/agent/context.js`). Mode
is injected into **every** LLM prompt — this is how Blu auto-tags
captured items (a todo added at 3pm gets `context: hexaware`
automatically) without ever asking the user to categorize.

## Request Lifecycle: A Single Message, End to End

1. **Meta Cloud API POSTs to `api/webhook.js`** with the raw WhatsApp
   payload (text message or interactive button/list reply).
2. **Button/list replies** route through `queueProcessor.js`'s
   `handleButtonAction` — a large if-chain matching on ID prefixes
   (`ltdone_`, `rdone_`, `rsnooze_`, `evnoted_`, `evsnooze_`,
   `rem_tonight_`, `rem_tmrw_`, etc.) that directly executes the
   corresponding memory operation without going through the LLM at all
   — button taps are inherently unambiguous.
3. **Free-text messages** hit the prefilter, then the brain if needed.
4. **The brain** (`src/agent/brain.js`) builds a context block
   (recent conversation, current mode, relevant semantic memory
   matches, GitHub PR/issue status, behavioral insights) and calls the
   LLM fallback chain with the system prompt + that context.
5. **The LLM returns strict JSON** — `{reply, action, data, confidence}`
   or `{reply, actions: [...], confidence}` for compound requests — the
   executor never parses freeform text, only this structured schema.
6. **Action executor** dispatches to the matching `memory.js` function
   (e.g. `add_todo` → `memory.addTodo(...)`).
7. **Proactive scan:** after executing, the brain checks context for
   things worth mentioning unprompted (related pending tasks, unreviewed
   learning count, an imminent event) and appends them to the reply.
8. **Reply sent** via `src/whatsapp/send.js` — plain text, a button
   message (≤3 quick replies), or a sectioned list message, depending on
   what the action calls for.

## Key Design Decisions

| Decision | Why | Tradeoff |
|----------|-----|----------|
| **Regex prefilter before LLM** | Zero-cost handling of common commands; keeps free-tier LLM rate limits for genuinely ambiguous messages | Prefilter rules must be kept in sync with the LLM's own intent vocabulary, or behavior diverges between the two paths |
| **JSON-mode structured output only** | Executor never has to parse freeform text — eliminates a whole class of "LLM said something reasonable-sounding but unparseable" bugs | Requires provider-specific handling — DeepSeek R1 doesn't support `json_object` mode, so its `<think>` blocks are stripped via regex post-processing instead |
| **Button actions bypass the LLM entirely** | Button taps are unambiguous by construction — no reason to pay LLM latency/cost to interpret `rdone_<uuid>` | Every new interactive component requires a corresponding hardcoded prefix handler in `queueProcessor.js` |
| **Mode injected into every prompt, not asked** | Zero-friction capture — Tarun never manually tags anything | Wrong mode at a boundary time (e.g. working late on SmartResQ past 11pm) mis-tags the item unless manually overridden |
| **Proactive surfacing after every action** | This is the actual "second brain" differentiator — a plain CRUD chatbot wouldn't do this | Adds LLM context-scanning overhead to every response; risk of the reply feeling noisy if surfaced items aren't well-filtered |

## Related Docs

See [LLM Brain](./03-llm-brain.md) for the fallback chain and system
prompt design in depth, and [Memory System](./04-memory-system.md) for
the Postgres schema this lifecycle reads and writes.

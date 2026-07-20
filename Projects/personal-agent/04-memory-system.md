# Memory System

## Mental Model

Memory is a **Postgres database playing three roles at once**: a
conventional relational store (todos, events), a vector store
(pgvector-backed semantic search over notes/learnings/knowledge), and a
lightweight application-state store (`state` table as a key-value blob
for mode override, push tokens, context summaries). Understanding which
role a given table plays clarifies why it does or doesn't have an
`embedding` column.

## Core Tables

| Table | Purpose | Has embeddings? |
|-------|---------|:---:|
| `todos` | Tasks with context, `remind_at`, done state | No (ILIKE fallback search) |
| `notes` | Freeform notes with auto-tags | Yes |
| `learnings` | Topic + content, reviewed flag, spaced repetition columns | Yes |
| `knowledge` | Permanent facts about Tarun's world (people, tools, projects) | Yes |
| `events` | Calendar events with recurrence | No |
| `conversations` | Rolling chat history (trimmed to 200 msgs weekly) | No |
| `skills` | User-taught skills Blu can execute | No |
| `goals` | Daily "One Big Thing" tracking | No |
| `user_insights` | Behavioral patterns, extracted every 20 messages | No |
| `reminders` | Time-based reminders | No |
| `state` | Key-value store (mode override, context summary, push tokens) | N/A |

## Schema Evolution (from `src/migrate_db.js`)

The migration file reveals meaningfully more sophistication than the
README's table list alone suggests:

1. **`pending_messages` + `dedup_messages`** — a message queue with
   per-message status, attempt count, and error tracking, plus a
   separate dedup table keyed on WhatsApp's `message_id`. This exists to
   handle Meta's at-least-once webhook delivery guarantee (duplicate
   webhook fires for the same message are common) and to make retries
   possible without reprocessing already-handled messages. *(Whether
   this queue is fully wired into the live request path or partially
   implemented wasn't confirmed during this pack's creation — verify
   `api/webhook.js` before assuming synchronous vs. queued processing.)*
2. **`todos.embedding vector(1536)`** — added via `ALTER TABLE`, meaning
   todos semantic search was a later addition, not part of the original
   schema (and note the dimension: 1536, not 768 — worth double
   checking against the `text-embedding-004` output dimension actually
   used elsewhere, since a mismatch would silently break similarity
   comparisons for this specific column).
3. **Memory decay on `knowledge`** — `last_referenced_at` and
   `flagged_for_review` columns imply a design intent for knowledge
   entries to be periodically revisited/pruned, similar in spirit to
   spaced repetition but for factual (not learning) memory.
4. **`entity_links`** — a lightweight entity graph: `entity_name`,
   `entity_type`, foreign-keyed to a `knowledge_id`. This is the
   substrate for cross-referencing (e.g. "Rohan" mentioned across
   multiple knowledge entries resolves to one entity).
5. **Spaced repetition on `learnings`** — `review_count`,
   `next_review_at`, `interval_days` — a genuine spaced-repetition
   scheduling mechanism, not just a "reviewed: true/false" flag as the
   README's simpler description might suggest.
6. **Prompt versioning + token tracking on `conversations`** —
   `prompt_version`, `user_satisfaction`, `tokens_input`,
   `tokens_output` columns, plus a standalone `prompt_versions` table
   storing the full prompt text per version. This is the infrastructure
   for **evaluating prompt changes against real usage** — comparing
   token cost and (if `user_satisfaction` is actually populated
   somewhere) subjective quality across prompt versions over time.

## Semantic Search

Notes, learnings, and knowledge entries are embedded with
`text-embedding-004` at write time. Queries use pgvector's cosine
distance operator (`<=>`) with a relevance threshold of 0.6. Results
above threshold are injected into the LLM's context block as "Relevant
past memory," which is the mechanism that lets Blu surface connections
across time (e.g. mentioning a months-old note when a related topic
comes up) — see [Proactive Features](./06-proactive-features.md) for how
this connects to the "Hermes layer" behavior.

**Todos fall back to ILIKE** (simple substring match) since they lack
embeddings in the base design — though the migration adding
`todos.embedding` suggests this gap may have been partially or fully
closed later. Confirm current behavior in `memory.js` before assuming
ILIKE-only search still applies.

## Conversation Trimming

Conversation history is capped and trimmed to the last 200 messages,
run as part of the weekly review cron (Sunday 8pm) rather than on every
write — a batch-cleanup approach that keeps the hot write path free of
trimming-logic overhead.

## Common Mistakes to Avoid When Extending This

- **Adding a new embeddable field without checking the actual embedding
  dimension in use elsewhere** — the `todos.embedding vector(1536)` vs.
  `text-embedding-004`'s native 768-dim output is a concrete example of
  where this can silently drift; pgvector doesn't error on a dimension
  mismatch until you actually try to compare vectors of different
  sizes.
- **Writing directly to `knowledge` without updating
  `last_referenced_at`** — breaks whatever decay/review logic consumes
  that column.
- **Bypassing `dedup_messages` when adding a new inbound message path**
  (e.g. a future non-WhatsApp channel) — would reintroduce the duplicate
  -processing problem this table exists to solve.

## Related Docs

[Reliability](./07-reliability.md) covers the dedup/queue tables from a
failure-handling angle. [LLM Brain](./03-llm-brain.md) covers how
semantic search results get woven into the prompt context.

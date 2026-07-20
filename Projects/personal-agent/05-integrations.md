# Integrations

## GitHub (`src/integrations/github.js`)

Fetches open PRs, open issues, and recent commits from the SmartResQ-dev
repo via the GitHub REST API, using a personal access token
(`GITHUB_TOKEN`) and configurable target repo (`GITHUB_REPO`). This data
is injected into every LLM context block, so Blu can surface PR status
proactively — e.g. mentioning an open PR during the evening mode-
transition brief without being asked. This is the concrete link between
Blu and [SmartResQ](../smartresq/) — Blu functions partly as a status
surface for that other project.

## Web Search (`src/integrations/search.js`)

Uses the Serper API (Google Search results) for the `search_web` intent.
Raw results are passed to the LLM to synthesize into a concise answer
rather than returned as a link list. Stable facts can be permanently
cached to the `knowledge` table via `cache: true` in the action data —
this means a repeated factual query can skip the Serper call entirely on
subsequent asks, trading a small amount of staleness risk for
eliminating repeat API cost/latency on stable facts (e.g. "what version
of Node does X require").

## Push Notifications (`src/push/push.js`)

Expo Push API integration for the companion mobile app. Notification
types: `reminder`, `brief`, `nudge`. Push tokens are stored in the
`state` key-value table (not a dedicated table) — worth knowing if
extending to multiple devices per user, since the current single-token-
per-key pattern likely doesn't support that without a schema change.

## Vision + Whisper (Stubs)

`src/integrations/vision.js` and `whisper.js` exist as integration
points for image understanding and audio transcription respectively, but
per the README are explicitly **not yet wired into the main flow**. They
appear referenced in `queueProcessor.js`'s imports
(`transcribeAudio`, `analyzeImage`), suggesting partial wiring exists at
the queue-processing layer even if not fully exposed through the intent
system — worth checking directly before assuming these are fully inert.

## Cross-Referencing (`src/integrations/connections.js`)

`findConnections` — referenced in `brain.js`'s imports alongside the
GitHub and search integrations. Purpose (per naming and context) is
surfacing connections across stored memory — likely the mechanism behind
part of the proactive "surface a related past note" behavior described
in [Proactive Features](./06-proactive-features.md), separate from the
pgvector semantic-search-triggered surfacing. Worth reading directly if
extending the proactive-surfacing logic, to avoid duplicating logic that
already lives here.

## Common Mistakes to Avoid When Adding a New Integration

- **Not injecting the new integration's data into the context block**
  — GitHub and search results are only useful to the proactive layer
  because they're part of what the LLM sees on every relevant call; an
  integration that's callable but not context-injected won't get
  surfaced proactively, only on explicit request.
- **Forgetting the caching pattern for stable external data** — the
  `cache: true` mechanism on web search results is a reusable pattern
  worth applying to any other external API call that returns
  slowly-changing data.

## Related Docs

[Proactive Features](./06-proactive-features.md) covers how integration
data actually surfaces in briefs and nudges. [Reliability](./07-reliability.md)
covers what happens when an integration call fails.

# Mobile App & Dashboard

## Mobile App (`mobile/`)

**Stack:** Expo SDK ~54, React 19, React Native 0.81, React Navigation
(bottom tabs). Distinct from `desktop/` (an Electron-style desktop
companion with its own `main.js`/`pet.js`) — two separate companion
clients exist alongside the primary WhatsApp interface.

**Screens:** `HomeScreen`, `ChatScreen`, `TodosScreen`, `NotesScreen`,
`CalendarScreen`, `PetScreen` — chat is a secondary interface to the
same brain (`handleIncomingStream` / streaming path from
[LLM Brain](./03-llm-brain.md)), while the other screens are more
direct CRUD views over the same Postgres memory WhatsApp writes to.

**The "Hex Pet" feature (`components/HexPet.js`, `screens/PetScreen.js`):**
a genuine gamification layer — animated pet sprite sheets exist for
distinct states: `idle`, `running` (left/right variants), `jumping`,
`waiting`, `waving`, `review`, and `failed`. This is a UX choice worth
noting explicitly: it's not just a utility app, there's an emotional-
engagement layer on top of the todo/memory system, likely tied to task
completion or streak state (confirm exact trigger logic in `HexPet.js`
before assuming specifics).

**Push notifications:** via `expo-notifications` + `expo-device`,
receiving the `reminder` / `brief` / `nudge` notification types sent
from `src/push/push.js` on the backend (see
[Integrations](./05-integrations.md)).

## Web Dashboard (`src/routes/dashboard.js` + `public/`)

A lightweight, token-gated (query param `?token=<DASHBOARD_TOKEN>`, not
a full auth system) analytics surface. Notable routes:

- **`/dashboard/test`** — a genuinely useful diagnostic endpoint that
  exercises the LLM brain, WhatsApp send, and GitHub API connectivity in
  one call and returns a structured ok/error result for each, plus an
  env-var presence check (booleans only, not values). This is the
  fastest way to answer "is the whole pipeline actually working right
  now?" without manually testing each piece — worth reaching for first
  when debugging a reported issue.
- **`/dashboard/api/todos`** — live todo list for sidebar refresh,
  presumably polled or hit on demand from the dashboard frontend.
- Additional analytics: todo stats, context (mode) breakdown, weekly
  activity charts, recent activity feed — backed by `getAnalytics()`,
  `getSummaryStats()` and related `memory.js` query functions.

**Mobile app's own REST API** (`src/routes/api.js`, distinct from the
dashboard routes) exposes chat, memory retrieval, and push-token
registration endpoints — the mobile app talks to this, not directly to
the dashboard routes.

## Common Mistakes to Avoid

- **Treating `DASHBOARD_TOKEN` as real authentication** — it's a single
  shared secret in a query string, adequate for a single-user personal
  tool but not a pattern to reuse if this ever became multi-tenant.
- **Forgetting the desktop app exists** when reasoning about "the
  companion client" — there are two (`mobile/` and `desktop/`), not one,
  and they may have diverged in feature parity.
- **Debugging pipeline issues manually** instead of hitting
  `/dashboard/test` first — it directly answers the three most common
  "is X actually broken" questions (LLM, WhatsApp send, GitHub) in a
  single request.

## Related Docs

[Integrations](./05-integrations.md) for the push notification backend
this app's `expo-notifications` setup receives from.

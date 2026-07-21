# Roadmap

## What "Work in Progress" Means Concretely Here

The README's own disclaimer is the most reliable roadmap signal
available for this project — rather than a stated feature backlog, it
identifies the specific accuracy gaps that need closing before this
could be considered anything beyond a reference/testing tool:

1. **NSE caching lag** (seconds-level) — likely acceptable as-is for a
   personal reference tool; only worth addressing if a lower-latency
   NSE access method becomes available.
2. **Yahoo Finance's unofficial ~15-minute delay** — this is the
   largest accuracy gap. A genuinely real-time upgrade path would
   require a paid/official market data provider for US futures, Sensex,
   forex, and commodities — a meaningful cost step up from the current
   fully-free-tier stack.
3. **Momentum score is an approximation**, not benchmarked against any
   commercial provider — if this score is ever used for anything beyond
   personal reference, it would need validation against a known-good
   technical-analysis source.
4. **News refresh (2 min) may miss breaking developments** — acceptable
   for a personal dashboard; would need a lower-latency feed (paid news
   API) for anything time-sensitive.

## Natural Next Steps (Inferred, Not Prescribed)

*(Reasonable directions given the stated limitations — not a committed
plan.)*

- **Cross-device preference sync** — currently, only auth syncs via
  Supabase; watchlist/theme/forex-card/commodity-card selections are
  device-local only (see [Architecture](./02-architecture.md)). Moving
  these into Supabase, keyed by user, would be a natural next step if
  multi-device usage becomes relevant.
- **Rate-limit resilience** — given reliance on free/unofficial APIs,
  adding explicit backoff/retry handling (if not already present in
  `server.js`) would improve resilience against occasional upstream
  throttling. *(Not directly confirmed whether this already exists —
  check `server.js` before assuming a gap.)*
- **A validated momentum score** — benchmarking the in-house RSI/MACD/
  ADX composite against a commercial provider's equivalent score would
  turn "an approximation" into a stated, quantified accuracy level.

## Project Applications / Why This Matters Beyond Itself

- **Honest latency documentation as a pattern** — the per-instrument
  latency table in [Architecture](./02-architecture.md) is a clean,
  reusable template for any project mixing data sources with different
  freshness guarantees: state the actual latency per source explicitly,
  rather than a single blanket "real-time" or "cached" claim for the
  whole system.
- **Framework-free frontend as a legitimate scale choice** — worth
  remembering as a counter-example whenever defaulting to a framework
  reflexively; a single-server, vanilla-JS app is a reasonable choice
  when the actual UI complexity doesn't warrant component-framework
  overhead.

## Retrospective

*(Fill in once there's more usage history — has the vanilla-JS choice
held up as features were added, has the localStorage-only preference
approach caused any real friction, has the 2-second polling interval
ever triggered rate limiting from NSE or Yahoo Finance.)*

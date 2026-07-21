# Overview

## What Is Institutional Dashboard?

A personal market intelligence terminal — per its own README, *"built
for institutional-grade trading data"* — displaying live Indian and US
market prices, standard pivot levels, in-house momentum scores, and a
curated financial news feed, with per-user accounts and customizable
watchlists stored in Supabase.

## The Honesty-About-Limitations Framing

The README leads with an explicit disclaimer rather than burying it —
worth preserving as a documentation practice, similar to
[QuickCover](../quickcover/07-deployment.md)'s live-vs-mocked table:

> *"Work in Progress — This project is actively being developed and is
> not production-ready. Data values may not reflect real-time market
> prices with full accuracy. Use for reference and testing purposes
> only, not for trading decisions."*

Specific accuracy caveats stated directly:
- **NSE India data** may lag several seconds due to API caching
- **US futures, Sensex, forex, commodities** come from Yahoo Finance's
  *unofficial* API, carrying a ~15-minute delay
- **Momentum scores** are in-house approximations from end-of-day data,
  not identical to any commercial data provider
- **News** is sourced from public RSS feeds, may not reflect breaking
  developments instantly

## Stack

| Layer | Technology |
|---|---|
| Backend | Node.js + Express |
| Frontend | Vanilla HTML / CSS / JS (no framework) |
| Database | Supabase (PostgreSQL) |
| Hosting | Railway, auto-deploy from GitHub |
| Data | NSE India official API, Yahoo Finance v8, RSS feeds |

**Notably framework-free on the frontend** — unlike
[QuickCover](../quickcover/)'s React Native/Vite stack or
[Personal Agent](../personal-agent/)'s Expo app, this project is plain
JS across `public/js/*.js` files (one per feature area: `market.js`,
`pivots.js`, `momentum.js`, `forex.js`, `commodities.js`, `news.js`,
`watchlist.js`, `theme.js`) — a reasonable choice for a project this
scoped, where a build step and component framework would add overhead
without a corresponding benefit at this scale.

## Maturity Level

Personal side project, actively developed, explicitly not
production-ready per its own framing. This is the smallest and least
architecturally complex of Tarun's four documented projects in this
Forge repo — see [Macro Platform](../macro-platform/) for a much larger
project in the same general domain (market/financial data) for
contrast.

## Key Constraint

**Be honest about data latency rather than presenting stale/delayed
data as real-time.** Every feature description in the README explicitly
states its actual latency characteristic (live, ~15min delay, cached
60min, refreshes every 2min) rather than a generic "real-time" claim —
this is the project's main design discipline given it's built entirely
on free-tier/unofficial data sources.

## Next: Architecture

See [Architecture](./02-architecture.md) for the API surface and data
source details.

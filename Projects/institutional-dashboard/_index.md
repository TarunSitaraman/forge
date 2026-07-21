# Institutional Dashboard Knowledge Pack

*A personal market intelligence terminal — live Indian and US market data, standard pivot levels, in-house momentum scores, and a curated financial news feed, with per-user accounts and watchlists.*

**Status:** Work in Progress — explicitly marked not production-ready by its own README. Use for reference/testing, not trading decisions.

**Tags:** #status/active #type/project #stack/nodejs #stack/express #stack/supabase

**Key Constraint:** Every data source is a free-tier/unofficial API with real latency limitations (NSE caching lag, Yahoo Finance's ~15-minute delay). The project is honest about this in its own README rather than presenting the data as real-time-accurate.

**Note on pack size:** this is a deliberately smaller knowledge pack than [SmartResQ](../smartresq/), [Personal Agent](../personal-agent/), [QuickCover](../quickcover/), or [Macro Platform](../macro-platform/) — a single Express server + vanilla JS frontend doesn't warrant the same document count as those larger systems. Depth here is scaled to the project's actual complexity, per Forge's Knowledge Pack convention.

## Quick Navigation

- **[Overview](./01-overview.md)** — What it is, the honesty-about-limitations framing, stack
- **[Architecture](./02-architecture.md)** — API routes, data sources and their latency characteristics, database schema
- **[Features](./03-features.md)** — Market summary, watchlist/pivots/momentum scoring, news feed, accounts, UI
- **[Roadmap](./04-roadmap.md)** — What "work in progress" means concretely here, next steps

## Key Facts

| Aspect | Detail |
|--------|--------|
| **Repo** | [github.com/TarunSitaraman/Institutional-Dashboard](https://github.com/TarunSitaraman/Institutional-Dashboard) |
| **Live URL** | web-production-ef52f.up.railway.app/login.html |
| **Stack** | Node.js + Express, vanilla HTML/CSS/JS, Supabase (Postgres) |
| **Hosting** | Railway, auto-deploy on push to `main` |
| **Data sources** | NSE India official API, Yahoo Finance v8 (unofficial), RSS feeds |

## Architecture At a Glance

```
Browser (vanilla JS, per-page: market/pivots/momentum/forex/commodities/news/watchlist)
  ↓
Express server (server.js) — single-file backend
  ↓
NSE India official API (live: Nifty 50, Bank Nifty, India VIX, Gift Nifty)
Yahoo Finance v8 (unofficial, ~15min delay: US futures, Sensex, forex, commodities, OHLC)
RSS feeds (ET Markets, Livemint, Zerodha Pulse — refreshed every 2 min)
  ↓
Supabase (Postgres) — users, sessions, per-user watchlists
```

## For Future You (Resuming Work on This Project)

Start with [Roadmap](./04-roadmap.md) — since this project is explicitly WIP, that doc is the most useful entry point for understanding exactly what "not production-ready" means concretely (which data is genuinely live vs. delayed, what's missing for real trading use).

---

**Source repo analyzed:** `TarunSitaraman/Institutional-Dashboard` @ `main`, via its full `README.md` (the primary and sufficient source for this pack — file tree confirms a small, single-server codebase matching the README's own description) (2026-07-21).

---

## Related Systems / Reference

- [`Technologies/Docs/`](../../Technologies/Docs/) — no existing canonical doc covers financial market data APIs or pivot/momentum technical-analysis formulas specifically; if this becomes a recurring domain across future projects, that would be the signal to create one (following the pattern noted in [Personal Agent](../personal-agent/)'s and [Macro Platform](../macro-platform/)'s packs for when a topic earns a standalone reference).
- [`Projects/macro-platform/`](../macro-platform/) — sibling project in the same domain (market/macro data); contrast this project's single-server simplicity against that platform's multi-tenant, governance-heavy architecture for two very different scales of the same general problem (serving financial data reliably).

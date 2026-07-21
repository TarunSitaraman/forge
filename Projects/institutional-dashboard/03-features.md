# Features

## Global Market Summary

- **US Futures** — S&P 500, Nasdaq 100, Dow Jones (price + points + %
  change, ~15 min delay)
- **Futures & Volatility** — Gift Nifty (live), India VIX (live,
  inverted color scale — high VIX reads as "bad" visually, matching how
  traders actually interpret it)
- **Indian Indices** — Nifty 50, Bank Nifty (live), Sensex (~15 min)
- **Currency** — dynamic forex card, add/remove from 10 pairs
  (USD/INR, EUR/INR, GBP/INR, JPY/INR, EUR/USD, GBP/USD, AUD/USD,
  USD/JPY, USD/CHF, USD/SGD), persisted per-device via localStorage

## Energy & Commodities

Dynamic commodity card, add/remove from 9 options (Brent Crude, WTI
Crude, Gold, Silver, Copper, Natural Gas, Wheat, Corn, Aluminium), with
a visual % change bar per commodity, persisted per-device.

## Market Lens (Watchlist)

- Add/remove NSE symbols by ticker or company-name search
- **OHLC table** — daily Open/High/Low/Close from previous session
- **Standard Pivot Levels** — S3-R3, cached in localStorage, refreshes
  on date change (see [Architecture](./02-architecture.md) for the
  formula)
- **Momentum Score** — the in-house 0-100 composite score with
  Bullish/Neutral/Bearish labeling (see
  [Architecture](./02-architecture.md) for the exact indicator mix)

## Live Intelligence (News Sidebar)

Curated headlines from ET Markets, Livemint, and Zerodha Pulse via RSS,
auto-tagged by category (Earnings, Economy, Policy, Global,
Commodities, IPO, Results). Refreshes every 2 minutes. Sidebar
collapses responsively on smaller screens.

## Account System

Register/login with email+password, HTTP-only session cookie (7-day
TTL), protected routes. See [Architecture](./02-architecture.md) for the
schema and session mechanics.

## UI

- Light/dark theme toggle, preference saved to localStorage
- Auto-refreshing market data (2-second interval during market hours)
- Flash animations on price changes (green up, red down) — a
  lightweight but high-value UX touch for a trading terminal, giving
  immediate visual feedback on price movement without requiring the
  user to compare numbers themselves

## Common Mistakes to Avoid When Extending This

- **Adding a new forex/commodity option without updating both the
  dropdown list and the localStorage persistence key** — the
  add/remove pattern here relies on a fixed enumerable option set per
  card type; a new instrument needs to be added consistently in both
  places or it'll silently fail to persist.
- **Auto-refreshing more aggressively than 2 seconds** without checking
  rate limits on the underlying free-tier APIs (NSE, Yahoo Finance) —
  these are unofficial/free sources without a documented rate-limit
  contract; more aggressive polling risks getting the server's IP
  throttled or blocked.

## Related Docs

[Overview](./01-overview.md) for the honesty-about-limitations framing
these features are built around. [Roadmap](./04-roadmap.md) for what's
still incomplete given the WIP status.

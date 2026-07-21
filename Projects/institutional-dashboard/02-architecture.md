# Architecture

## API Routes (Verified from README)

| Route | Returns | Source | Latency |
|---|---|---|---|
| `GET /api/quote/:symbol` | `{ price, change, changePct }` | Yahoo Finance v8 | ~15 min |
| `GET /api/nse/indices` | `{ nifty50, niftyBank, indiavix }` | NSE India official | Live |
| `GET /api/nse/giftnifty` | `{ price, changePct, expiry, timestamp }` | NSE India official | Live |
| `GET /api/ohlc/:symbol` | Previous session `{ O, H, L, C }` | Yahoo Finance v8 | End of day |
| `GET /api/momentum/:symbol` | `{ score, label, components }` | Calculated in-house, 2yr Yahoo data | Server-cached 60 min |
| `GET /api/news` | `[{ title, publisher, link, publishedAt, category }]` | RSS (ET Markets, Livemint, Zerodha Pulse) | Refreshes every 2 min |
| `POST /api/auth/register` | `{ ok }` | Supabase | — |
| `POST /api/auth/login` | Sets session cookie | Supabase | — |
| `POST /api/auth/logout` | Clears session cookie | Supabase | — |
| `GET /api/auth/me` | `{ email, name }` | Supabase | — |

## Data Source Latency Table (Full, from README)

| Instrument | Source | Latency |
|---|---|---|
| Nifty 50, Bank Nifty, India VIX | NSE India official (`allIndices`) | Live |
| Gift Nifty | NSE India official (`marketStatus`) | Live |
| S&P 500, Nasdaq, Dow Jones futures | Yahoo Finance v8 | ~15 min |
| Sensex | Yahoo Finance v8 (`^BSESN`) | ~15 min |
| Forex pairs | Yahoo Finance v8 | Near real-time |
| Commodities | Yahoo Finance v8 | ~15 min |
| OHLC (pivots) | Yahoo Finance v8 | End of day |
| Momentum score | In-house, 2yr daily OHLC | End of day, cached 60 min |
| News | ET Markets / Livemint / Zerodha Pulse RSS | Every 2 min |

**The clear split worth noting:** anything sourced from **NSE India's
official API is live**; anything from **Yahoo Finance's unofficial API
carries the ~15-minute delay**. This single distinction explains most of
the latency characteristics across the whole feature set — worth
remembering as the one mental shortcut for "is this number current."

## Database Schema (Supabase)

```sql
users     (id, email, name, password_hash, created_at)
sessions  (id, token, user_id, expires_at, created_at)
```

Minimal by design — auth/session only. Watchlist preferences and
theme/forex/commodity card selections are persisted via **localStorage
per-device**, not in Supabase — meaning a user's customization doesn't
sync across devices, only their login credentials do. Worth confirming
this is an intentional scope decision before assuming watchlist sync
is a planned feature.

## Momentum Score Calculation (In-House)

Composite 0-100 score from 2 years of daily Yahoo Finance data:
**RSI(14) · MACD · ADX(14) · Price vs. SMA20/50/200 · 52-week position
· Volume trend** — mapped to a label (Technically Bullish / Neutral /
Bearish). Server-cached 60 minutes, refreshing at market open (09:15
IST) and close (15:30 IST).

## Pivot Levels

Standard formula from previous session OHLC:
`P = (H+L+C)/3`, with S3/S2/S1/Pivot/R1/R2/R3 derived from the range.
Cached in localStorage, refreshes on date change.

## Account System

- Register/login with bcrypt-hashed passwords (10 rounds)
- HTTP-only session cookie (`sn-session`, 7-day TTL) stored in Supabase
- Protected routes redirect unauthenticated requests to `/login.html`

## Common Mistakes to Avoid When Extending This

- **Presenting Yahoo Finance data as real-time** anywhere in the UI —
  the ~15-minute delay is a hard characteristic of the unofficial API,
  not a caching choice that could be tuned away.
- **Adding a new per-user preference to localStorage instead of
  Supabase** without checking whether cross-device sync is actually
  wanted for that specific preference — the current split (auth in
  Supabase, everything else in localStorage) may or may not be
  intentional for new features.

## Related Docs

[Features](./03-features.md) for how these routes and data sources
compose into the actual UI.

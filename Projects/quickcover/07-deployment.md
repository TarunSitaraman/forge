# Deployment

## Live Endpoints

| Service | Platform | URL |
|---|---|---|
| Backend API | Render | quickcover.onrender.com |
| Mobile App (Web) | Vercel | quick-cover-865h.vercel.app |
| Admin Dashboard | Vercel | quick-cover-neon.vercel.app |
| Android APK | EAS Build | direct download link in README |
| Database | Supabase | PostgreSQL (pooler, ap-northeast-2) |

## What's Live vs. Mocked (Verbatim Honesty From the README)

This table is worth preserving directly — it's a strong example of
honest scoping in a hackathon README, distinguishing genuinely wired
integrations from demo scaffolding rather than blurring the two:

| Component | Status |
|---|---|
| Backend API (all endpoints) | ✅ Live on Render |
| Auth (signup/login/JWT) | ✅ Live (bcrypt + JWT, 30-day tokens) |
| Mobile app (all screens) | ✅ Live via Expo Go, connects to live backend |
| Admin dashboard | ✅ Live on Vercel |
| Dynamic pricing engine | ✅ Live — real OpenWeatherMap weather+AQI → surcharge |
| Parametric triggers (rain/heat/AQI) | ✅ Live — 3 triggers via OpenWeatherMap, auto-evaluate every 60s |
| Platform outage trigger (4th) | ✅ Live — admin webhook + 90-min threshold |
| Zero-touch claims | ✅ Live — auto-creates claims for eligible workers |
| Shift-level payout cap | ✅ Live — 8-hour dedup check |
| UPI payout (Razorpay) | ✅ Live — real transaction IDs |
| Fraud detection | ✅ Live — 3-tier Isolation Forest scoring |
| GenAI vision adjudication | ✅ Live — Gemini 1.5 Flash via `/claim/adjudicate` |
| IMD direct API | 🟡 Post-hackathon (using OpenWeatherMap instead — IMD has limited programmatic access) |
| **Delivery platform trip verification** | 🟡 Post-hackathon — see caveat below |

## The One Honest, Load-Bearing Caveat

**Trip legitimacy is not externally verified.** Workers can start/complete
trips freely in the app with no cross-check against a real Blinkit/Zepto/
Swiggy delivery event. The 25-trips/7-days eligibility threshold acts
only as a soft spam deterrent, and the Isolation Forest fraud model
(GPS/telematics/timing anomalies) is a secondary control — but **neither
is a substitute for platform-level trip verification**, which the README
itself identifies as the primary control that must exist before any real
payout system goes live. Without it, a driver could tap Start/End Trip
repeatedly to satisfy the eligibility threshold without a single real
delivery.

This is the single most important gap to close before this system could
handle real money at scale — see [Roadmap](./09-roadmap.md).

## Local Development

```bash
# Backend
cd mock-backend && npm install && npm start   # http://localhost:4000

# Mobile
cd mobile && npm install && npx expo start

# Admin
cd admin-frontend && npm install && npm run dev
```

## Common Mistakes to Avoid

- **Assuming "mock-backend" folder name implies non-production code** —
  most of what's inside is genuinely live in production (see the table
  above); the name is a hackathon-era artifact, not a status indicator.
- **Treating the eligibility threshold as fraud prevention** — it's
  explicitly a spam deterrent only; real fraud prevention is the
  Isolation Forest + GenAI adjudication layers, and even those don't
  cover fabricated trips (see the caveat above).

## Related Docs

[Roadmap](./09-roadmap.md) for the full phase-by-phase build log this
live/mocked split is drawn from.

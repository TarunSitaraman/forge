# Backend & Admin

## Backend API Surface (`mock-backend/server.js`, verified via route grep)

| Route | Purpose |
|---|---|
| `GET /health` | Health check |
| `GET /status` | Worker/trip status |
| `GET /eligibility` | Coverage eligibility check (25 trips / 7 days threshold) |
| `GET /trips/recent` | Recent trip history |
| `POST /accept-trip` | Worker accepts a trip — activates coverage |
| `POST /complete-trip` | Worker completes a trip |
| `POST /trigger-disruption` | Manually fire a disruption (demo/testing) |
| `POST /reset` | Reset demo state |
| `POST /claim/adjudicate` | GenAI vision adjudication endpoint (Model 3) |
| `POST /refresh-forecast` | Trigger pricing/forecast refresh |
| `POST /pricing/zone` | Zone-level dynamic pricing (XGBoost, Model 1) |
| `POST /admin/zone-outage` | Admin: mark a zone as outage-affected (4th trigger type) |
| `GET /admin/zone-outages` | List active zone outages |
| `POST /cron/evaluate-live-triggers` | Scheduled job: poll weather/outage triggers, auto-create claims |
| `GET /admin/analytics` | Loss ratio / pool analytics for admin dashboard |
| `GET /stats` | General stats |

**`POST /cron/evaluate-live-triggers` is the zero-touch claims engine**
— this is the endpoint that implements the "no manual claims" promise
end to end: it polls live weather/outage data, checks trigger
thresholds, and auto-creates claims for eligible active workers in
breached zones without any user action. This is architecturally the
most important single endpoint in the system.

## Key Backend Modules (`mock-backend/`)

| File | Role |
|---|---|
| `auth.js` | Auth (JWT, bcrypt) |
| `database.js` | Dual-mode DB layer (SQLite locally / Postgres in prod) |
| `pricing_model.json` + `train_model.js` | XGBoost pricing model artifact + training script |
| `fraud_scoring.js` | Isolation Forest 3-tier fraud scoring |
| `genai_adjudication.js` | Gemini vision adjudication (Model 3) |
| `live_parametric_triggers.js` | Weather/outage polling + trigger evaluation |
| `trigger.js` | Trigger definitions/thresholds |
| `test_apis.js` | API test harness |

## Database Schema (Inferred from README + Route Surface)

Confirmed tables referenced in the README's Phase timeline:
- `policy_sessions` — per-trip coverage records (added Phase 2)
- `zone_outages` — 4th parametric trigger type (platform outage)
- Core trip/claim/user tables (exact schema not directly confirmed —
  read `mock-backend/database.js` before making schema assumptions)

## Admin Dashboard (`admin-frontend/`)

Vite + React + TypeScript, deployed to Vercel. Per the README's feature
list, provides:
- **Claims pipeline** — review/ops console for claims
- **Loss ratio gauge** — actuarial health at a glance
- **Predictive analytics chart**
- **Zone Outage Manager** panel — manually mark platform outages
- **Cron Eval panel** — presumably a manual trigger/inspection UI for
  the `/cron/evaluate-live-triggers` job
- **Pricing Engine dropdown** — per-zone pricing override/inspection

## Common Mistakes to Avoid When Extending This

- **Adding a new trigger type without updating both
  `live_parametric_triggers.js` AND the shift-level payout cap check**
  — the 8-hour dedup logic (see [Financial Model](./03-financial-model.md))
  must apply per disruption *type*, so a new type needs its own
  dedup key, not automatic inclusion in an existing one.
- **Calling `/claim/adjudicate` without first checking the Isolation
  Forest tier** — Model 3 (GenAI) is specifically for the *quarantine*
  tier (0.46-0.70 anomaly score); routing everything through vision
  adjudication defeats the auto-approve/auto-reject fast paths.

## Related Docs

[AI/ML Models](./04-ai-ml-models.md) for what each of these endpoints
computes. [Deployment](./07-deployment.md) for how this backend is
actually hosted and what's genuinely live vs. demo scaffolding.

# Roadmap

## Development Phase Timeline (As Documented in the README)

| Phase | Focus | Key Deliverables |
|---|---|---|
| **Phase 1 — Foundation** | Core data model, auth, trip lifecycle | Dual-mode Postgres/SQLite schema; JWT auth; `/accept-trip`/`/complete-trip`/`/status` API; React Native app (Home/Claims/Coverage/Profile tabs); `MockDataContext` polling live backend |
| **Phase 2 — Intelligence** | Live APIs, financial safeguards, admin tools | Live OpenWeatherMap weather+AQI triggers; per-zone dynamic pricing; `policy_sessions` table; `zone_outages` table + 4th trigger; zero-touch `runTriggerEvaluation()`; 8-hour shift-level payout cap; admin Zone Outage Manager + Cron Eval panels; `CoverageHonoredCard` mobile UI |
| **Phase 3 — Soar ✅ COMPLETE** | AI/ML, fraud, GenAI, payments | XGBoost pricing model; Isolation Forest fraud scoring; Gemini vision adjudication; live Razorpay UPI payouts with real transaction IDs; admin loss-ratio gauge + predictive analytics; mobile photo-upload claim evidence; `FINANCIAL_MODEL.md`; Android APK (EAS build) |

Six weeks total, prioritizing a working end-to-end demo over feature
breadth — each phase has deliverables a judge/reviewer can verify
directly against the live deployment URLs, not just described in text.

## Confirmed Gap: Platform-Level Trip Verification

The single most important post-hackathon item — see
[Deployment](./07-deployment.md) for the full explanation. Without
cross-referencing trip records against a verified event from the actual
gig platform (Blinkit/Zepto/Swiggy) via partner API or webhook, the
eligibility gate can be gamed by tapping Start/End Trip with no real
delivery. This is a blocker for handling real money, not a nice-to-have.

## Confirmed Gap: IMD Direct API

Currently using OpenWeatherMap as a substitute — IMD (India
Meteorological Department) has limited programmatic access. Worth
revisiting if a more authoritative/official weather data source becomes
available, both for accuracy and for regulatory credibility (IMD is the
government's own data source).

## Natural Next Steps (Inferred, Not Prescribed)

*(Reasonable directions given the current state — not a committed plan.)*

- **Platform partnership conversations** (Blinkit/Zepto/Swiggy) — per
  [Market Research](./08-market-research.md)'s own verdict, this is the
  primary execution risk, ahead of any further product work.
- **Shadow-mode validation on real trip data** before scaling past
  demo/hackathon usage, to calibrate the payout rate and eligibility
  threshold against real (not modeled) disruption frequency.
- **Formal reinsurance partner engagement** — the Aggregate Stop-Loss
  treaty is currently a modeled strategy, not a signed treaty; this
  needs a real counterparty before cluster-risk protection is genuine
  rather than theoretical.
- **IRDAI sandbox application** — positioning as a technology
  distribution layer over a licensed insurer requires an actual
  insurer partnership, not just the intended structure.

## Project Applications / Why This Matters Beyond Itself

- **Parametric insurance design pattern** — the trigger→verify→payout
  pipeline here is a clean, reusable reference for any "pay automatically
  on objective external data" system, insurance or otherwise.
- **Honest scoping in hackathon documentation** — the live-vs-mocked
  table and the explicit trip-verification caveat are a good example of
  documentation discipline worth replicating in future hackathon/demo
  projects: say clearly what's real, what's staged, and what the single
  most important remaining gap is.
- **Three-model ML pipeline with clear separation of concerns** — worth
  referencing whenever designing a system where pricing, fraud
  detection, and adjudication are conflated into one model by default;
  QuickCover's explicit three-way split (see
  [AI/ML Models](./04-ai-ml-models.md)) is a clean counter-example.

## Retrospective

*(Fill in once there's distance from the hackathon — what worked in
the 6-week sprint model, what you'd prioritize differently, what
surprised you about building parametric insurance specifically.)*

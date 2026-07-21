# Market Research

*Research conducted April 2026, per the README's own dating — treat
figures here as of that date, not as live market data.*

## The Parametric Model Is Already Proven in India

QuickCover is not a theoretical mechanism — the core parametric-payout
pattern has been validated at real scale by **SEWA (Self-Employed
Women's Association)**: parametric heat insurance launched 2023 with
21,000 women in Gujarat, expanded to **225,000 workers across 7 states
by 2025**. In 2024, 92% of insured members (46,242 women) received
automatic cash payouts when temperature thresholds were breached —
₹2.92 crore disbursed with **zero claims process**.

QuickCover is this exact model, applied to a different trigger set
(monsoon/AQI/outage instead of heat-only) and a different cohort
(Q-commerce delivery partners instead of informal women workers). The
architecture isn't novel; the application to this specific cohort is.

## Market Size (2025-2026 Actuals vs. Original README Estimates)

| Metric | Original Estimate | 2025-2026 Actual |
|---|---|---|
| Active Q-commerce delivery partners | 200,000-300,000 | **450,000-500,000** (↑70-80% YoY) |
| Blinkit daily orders | 750,000-1,000,000 | ~**1,000,000** |
| Blinkit market share | ~45% | **48%** |
| Swiggy Instamart share | ~25% | **24%** |
| Zepto share | ~22% | **22%** |

**The addressable market is larger than modeled** — the financial
projections in [Financial Model](./03-financial-model.md) (built at 10%
rollout against the original, smaller estimate) are conservative
relative to actual 2025-2026 market size.

## Regulatory Tailwinds

1. **Code on Social Security 2020** — implemented November 2025.
   Requires platforms to contribute to a central social security fund;
   Zomato, Blinkit, Urban Company, and Uncle Delivery have registered.
   Creates the legal infrastructure QuickCover's model slots into.
2. **Budget 2025** — health insurance for 1 crore gig workers under
   PMJAY announced, signaling gig-worker welfare as a policy priority.
3. **IRDAI Insurance Products Regulations 2024** — an explicit
   "innovative products" sandbox. QuickCover positions as a technology
   distribution layer on top of a licensed insurer, not a direct
   insurer — no own IRDAI license required initially.
4. **State legislation** — Rajasthan (2023), Karnataka, and Bihar have
   enacted gig-worker protection laws; the trend is toward *mandatory*
   platform contributions, which QuickCover's consumer-funded model
   pre-empts.

## Business Model Risks & Mitigations

| Risk | Severity | Mitigation |
|---|---|---|
| **Platform dependency** — Blinkit/Zepto must embed the checkout surcharge | High | Position as a branded retention feature ("protect your rider"); carbon-offset-style consumer acceptance precedent |
| **Cluster risk** — simultaneous claims across a zone in a severe event | Medium | Aggregate Stop-Loss Reinsurance treaty (see [Financial Model](./03-financial-model.md)) |
| **Actuarial calibration** — payout rate/eligibility threshold need real validation | Medium | SEWA pilot data as benchmark; 6-month shadow mode on real trip data before going live |
| **IRDAI licensing** — operating as insurance without a license | Low | Surcharge framed as "protection fund contribution" (consumer = donor, driver = beneficiary); partner with a licensed insurer |
| **Consumer opt-out** | Low | Default opt-in at checkout; Swiggy's rain-fee precedent shows Indian consumers accept delivery surcharges with minimal pushback |
| **Driver fraud (GPS spoofing)** | Low | Isolation Forest + mock-location flags + telematics (see [AI/ML Models](./04-ai-ml-models.md)); eligibility gate excludes casual bad actors |

## Verdict (Per the README's Own Assessment)

The model is commercially feasible and technically validated: the
parametric mechanism works in India at real scale (SEWA), the market is
growing faster than originally modeled, and regulatory infrastructure is
being built by government, not against the model. **The primary
execution risk is platform partnership (getting Blinkit to actually
embed the checkout surcharge), not product design or regulation.**

## Related Docs

[Financial Model](./03-financial-model.md) for the unit economics this
research validates. [Roadmap](./09-roadmap.md) for what a path past the
hackathon toward real platform partnership would require.

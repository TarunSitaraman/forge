# Financial Model

## Consumer Micro-Charge (AI-Variable, Never Flat)

| Order Value | Surcharge | As % of Order |
|---|---|---|
| ₹100–₹300 | ₹2 | 0.7–2% |
| ₹300–₹700 | ₹3 | 0.4–1% |
| ₹700–₹1,500 | ₹5 | 0.3–0.7% |

The surcharge is priced per-order by the XGBoost pricing model (see
[AI/ML Models](./04-ai-ml-models.md)) using live weather risk, zone
disruption history, driver shortage, time of day, and current pool
balance — a sunny low-risk day can price as low as ₹1.50; an active
monsoon warning can surge it to ₹5.00 to adequately fund the risk pool.

## Payout Triggers (Parametric — Fully Automatic)

| Trigger | Condition | Driver Payout |
|---|---|---|
| Heavy rain | IMD/OpenWeatherMap: >15mm/hr in zone | ₹300–500/shift |
| Extreme heat | >43°C for 2+ hrs | ₹250–400/shift |
| Platform outage | Zone unavailable >90 mins | ₹200–350 |
| Lockdown / curfew | Govt. notification | ₹500–700/day |

## Shift-Level Payout Capping — The Key Financial Safeguard

**One payout per disruption type per 8-hour shift window.** Before
releasing any payout, the backend queries the worker's trip history for
a prior payout of the *same disruption type* within the last 8 hours. If
found, the event is logged as `"Verified Disruption — Coverage
Honored"` but no second transfer fires.

**Why this exists:** a single sustained monsoon event can persist 6-12
hours. Without this cap, one weather event could trigger dozens of
payouts to the same worker across a single shift, blowing out the loss
ratio. This is the single most important actuarial guardrail in the
whole system — without it, the parametric model's automatic-payout
convenience becomes its own insolvency risk.

## Catastrophe Risk & Reinsurance Strategy

Parametric triggers create **cluster risk**: a single severe cyclone or
city-wide flood can trigger thousands of simultaneous claims in one
zone, overwhelming a micro-pool sized for normal disruption frequency.

**Mitigation — Aggregate Stop-Loss Reinsurance treaty:** once claim
volume in a zone exceeds the 99.5% confidence interval (TailVaR) for
expected disruptions, a reinsurance partner covers the tail risk. This
lets QuickCover price premiums for the *expected* loss scenario while
offloading Black Swan exposure — the same structural pattern
catastrophe bond markets use globally.

## Unit Economics (10% Rollout Scenario)

| Metric | Value |
|---|---|
| Blinkit orders/day (India) | ~750,000–1,000,000 |
| Avg surcharge per order | ₹3 |
| Monthly pool inflow (10% rollout) | ₹6.7Cr–₹9Cr |
| Monthly driver payouts | ₹1.75Cr–₹3.5Cr |
| Loss ratio | ~30–50% (target 55–65%) |
| QuickCover platform margin | 15–20% of pool |

**Break-even at ~2-3% of Blinkit's daily order volume participating.**

## Weekly Pool Math (Worked Example)

```
Weekly pool inflow = orders/day × avg surcharge × 7 days × rollout %
                   = 750,000 × ₹3 × 7 × 10%
                   = ₹1,575,000  (~₹1.575 Crore/week)

Driver payout pool (62% allocation) = ₹9,76,500/week
Drivers covered at 10% rollout      = ~25,000
Per-driver funded allowance/week    = ₹391/driver/week

Expected actual payout/driver/week (probabilistic):
  0.5–1.0 disruption events/week × ₹350 avg payout = ₹175–350

Pool surplus at target loss ratio: ₹41–₹216/driver/week → actuarially sound
```

This is the core proof that per-trip micro-surcharges are actuarially
equivalent to a traditional weekly premium — ₹391/driver/week collected
invisibly from consumers, not workers, at the modeled participation
rate.

## Strict Policy Exclusions (Systemic Risk Management)

To protect pool solvency, QuickCover explicitly excludes:
- **Health, life, vehicle repair costs** — require separate dedicated
  actuarial pools, outside parametric income-protection scope.
- **War, Terrorism, Nuclear/Biological hazards** — force-majeure events
  at this scale are uninsurable within a localized micro-pool.
- **Global/National Pandemics** — a systemic shutdown (COVID-19 scale)
  would hit 100% claim frequency across all zones simultaneously with
  no compensating premium inflow; no micro-pool can fund that.

These exclusions are as important architecturally as the coverage
itself — they're what keeps the pool's risk model tractable.

## Related Docs

[AI/ML Models](./04-ai-ml-models.md) for how the surcharge is actually
priced per-order. [Market Research](./08-market-research.md) for
external validation of this model (SEWA's parametric heat insurance
precedent) and the identified business risks.

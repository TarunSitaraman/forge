# QuickCover Overview

## What Is QuickCover?

A consumer-funded parametric income protection platform for India's
Q-commerce delivery partners (Blinkit, Zepto, Swiggy Instamart). Built
for and submitted to **Guidewire DEVTrails 2026: Unicorn Chase**.

**The one-line pitch:** protection is funded entirely by a micro-
surcharge on the *consumer's* order (₹2-5, less than the cost of a
single Mentos) — the driver never pays anything, and payouts fire
automatically on objective, verifiable disruption events with zero
manual claims process.

## The Problem It Solves

India's 15 million+ gig delivery workers power the Q-commerce economy
with zero financial protection when it breaks down around them. A
single disrupted shift (flood, extreme heat, unplanned curfew) erases
₹400-650 of net income — 20-30% of a ₹10k-20k monthly take-home — with
no recourse. Platforms already spend heavily on accident/hospitalization
insurance (Blinkit: ₹100+ crore/year), but nothing covers income lost to
weather, outages, or zone disruptions. That gap is the product.

## The Core Insight

```
Consumer places ₹500 order → ₹3 surcharge added at checkout
  ↓
Worker accepts the order → Coverage activates for that trip
  ↓
External disruption detected (heavy rain / outage / curfew)
  ↓
AI verifies: worker GPS + platform logs + trigger event data
  ↓
Payout auto-credited → Worker's UPI wallet
```

No claims paperwork. No cost to the driver — ever. This is
**parametric** insurance: payout is triggered by an objective,
externally-verifiable data threshold (e.g. IMD rainfall >15mm/hr for
20+ minutes), not a subjective adjuster decision.

## Concrete Walkthrough: Arjun's Day

The README documents an exact, timestamped scenario rather than an
abstract description — worth preserving here as the canonical mental
model for how the product actually behaves end-to-end:

| Time | Event |
|---|---|
| 09:15 | Arjun accepts Trip #1142 on Blinkit. Coverage activates the moment he taps Accept. |
| 10:47-10:51 | IMD sensor records 22mm/hr rainfall for 4+ minutes — parametric trigger fires, Zone B marked `DISRUPTED`. |
| 10:52 | App shows a disruption alert card; Arjun taps "File a Claim." |
| 10:54 | Arjun selects disruption type, submits — claim status `Under Review`. |
| 11:19 | AI cross-verification (GPS stationary + accelerometer zero-movement + independent IMD confirmation) completes in 25 minutes. Claim approved. |
| 11:24 | ₹450 credited via Razorpay — **37 minutes total, flood to wallet.** |

This scenario is the product's own chosen proof point — any explanation
of "how does QuickCover work" should reference this exact timeline
rather than a generic description, since it's specific enough to be
falsifiable/demoable.

## Why Per-Trip Surcharges, Not a Weekly Premium

A common objection from insurance professionals: "where's the weekly
premium?" QuickCover's answer is structural equivalence, not absence —
micro-premiums on every covered trip aggregate into the same weekly
pool a traditional premium would, but with better properties for this
specific cohort:

| Dimension | Traditional Weekly Premium | QuickCover Per-Trip |
|---|---|---|
| Who pays | Driver (deducted from earnings) | Consumer (added to order) |
| Premium amount | Fixed regardless of activity | Variable, proportional to trips |
| Coverage gap | Full week charged even if driver works 1 day | Zero premium on non-working days |
| Driver experience | Feels like a deduction | Invisible — driver never sees it |
| Adverse selection | High (sick drivers still pay) | Low (activates only on accepted trips) |

**Why this matters for irregular income:** a gig worker who works 3
days one week and 7 the next can't budget a fixed premium the way a
salaried worker can. Per-trip surcharges make the pool self-balancing —
it grows exactly when activity (and therefore risk exposure) is high.

## Maturity Level

**Hackathon-complete across 3 planned phases** (Foundation → Intelligence
→ Soar), with Phase 3 explicitly marked done in the repo. Live
deployments exist for backend (Render), mobile web (Vercel), and admin
dashboard (Vercel), plus a downloadable Android APK via EAS Build. See
[Roadmap](./09-roadmap.md) for the exact "what's live vs. mocked"
breakdown — the project is explicit and honest about which pieces are
real integrations versus demo scaffolding, which is worth preserving as
a documentation practice.

## Key Constraint

**Zero cost to the driver, always.** Every other design choice —
dynamic AI-priced surcharges, parametric (not adjuster-based) payouts,
shift-level payout capping, reinsurance for tail risk — exists to make
this constraint solvent at scale without ever relaxing it.

## Next: Architecture

See [Architecture](./02-architecture.md) for the system diagram and the
mobile-first rationale.

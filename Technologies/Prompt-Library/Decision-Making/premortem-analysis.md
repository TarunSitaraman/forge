# Pre-Mortem Analysis

*Purpose:* Surface the specific, plausible ways a plan could fail by imagining the failure has already happened — a technique that produces sharper risk analysis than asking "what could go wrong" directly.

## When to use
Before committing to a significant plan/decision, especially one the team feels confident about (confidence is exactly when a pre-mortem is most valuable).

## Expected input
The plan/decision as currently intended, and the timeframe over which it would play out.

## Expected output
A ranked list of specific, plausible failure stories, each with an early warning sign and a preventive action — not generic risk categories.

## The complete prompt
```
Run a pre-mortem on this plan. Imagine it is {timeframe} from now, and the
plan has failed. Work backward from that failure.

Plan: {description}
Timeframe: {when we'd know if it failed}

Generate 4-5 distinct, specific failure stories — each a plausible
narrative of HOW it failed (not a generic category like "poor
execution"). For each:
1. The specific failure narrative — what happened, in what order,
   starting from a plausible trigger.
2. The earliest observable warning sign that this failure mode was
   starting — something checkable well before the timeframe is up.
3. A specific preventive action we could take now to reduce this
   failure's likelihood or catch it earlier.

Rank the 4-5 failure stories by a combination of likelihood and severity,
most important first. Do not include a failure story that's really just a
restatement of another one with different words.
```

## Variants
- **Launch/release variant:** frame timeframe as "one week post-launch" and bias failure stories toward user-facing and operational failures.
- **Strategic-bet variant:** frame timeframe as 6-12 months out and bias toward market/competitive failure stories.

## Related prompts
- [[decision-tradeoff-matrix]]
- [[production-incident-triage]]

## Tips
- Run this precisely when the team feels most confident — that's when blind spots are least likely to be caught any other way.
- Insist on specific narratives, not generic risk categories; "poor communication" is not actionable, "the vendor's API changes without notice and no one monitors for it" is.

## Common mistakes
- Generating generic risk categories instead of specific causal narratives, producing a list that reads as if from a risk-management template.
- Skipping the "early warning sign" step, which is what actually makes a pre-mortem useful — without it, the risks are just noted, not monitored.

## Example
Input: plan = "launch a new pricing model in 2 months," timeframe = "3 months post-launch."
Expected output: a failure story where existing customers churn because the migration path wasn't communicated clearly, with the early warning sign being a spike in support tickets in week 1, and the preventive action being a proactive migration email with a grace period.

# Offer Comparison Reasoning

*Purpose:* Reason through a real multi-offer (or offer-vs-stay) decision
using your actual weighted priorities, surfacing what would flip the
decision — instead of anchoring on whichever number is biggest.

## When to use
When you have two or more real offers, or one real offer to weigh against
staying, and want to pressure-test your instinct before deciding.

## Expected input
The filled-in `Career/offer-comparison-matrix.md` (or equivalent
data: comp, role, team, growth, stability per option), and your gut
instinct so far.

## Expected output
A reasoned comparison against your stated priorities, the condition that
would flip the decision, and an honest check on whether your gut
instinct matches what you say you value.

## The complete prompt
```
Help me reason through this offer decision using my actual priorities —
don't just compare total comp numbers.

Options: {paste the filled comparison matrix — comp, role, team, growth,
stability, location per option, including "stay" as an option if
relevant}
My stated priorities, ranked: {what actually matters most to you right now}
My gut instinct so far: {which way you're currently leaning, and why}

Reason through:
1. Score each option against my STATED priorities (not comp alone) —
   show the reasoning per priority, not just a final score.
2. Check whether my gut instinct matches what the weighted scoring
   suggests. If they diverge, that's worth surfacing explicitly — it
   often means either my stated priorities aren't actually my real
   priorities, or I'm underweighting something in my gut reaction.
3. State the specific condition that would flip the recommendation
   (e.g. "if growth trajectory were weighted higher than compensation,
   Offer B wins instead") — this is often more useful than the raw
   recommendation.
4. Name the single biggest risk of the leading option, even though it's
   ahead.
5. If "stay" is a real option, make sure it's compared on equal footing,
   not treated as an implicit default.

Give a clear recommendation, but make the reasoning and its sensitivity
to my stated weights fully visible.
```

## Variants
- **Multiple-competing-offers variant:** add a pairwise comparison step between the top 2 if 3+ options are close.
- **Values-clarification variant:** if gut instinct and weighted score diverge sharply, add an explicit prompt to re-examine whether the stated priority list is actually honest.

## Related prompts
- [[decision-tradeoff-matrix]]
- [[salary-research-prompt]]

## Tips
- Provide your real, ranked priorities honestly — a comparison run against priorities you think you SHOULD have (vs. what you actually weight) will misdirect the recommendation.
- Take a gut-vs-score divergence seriously as signal, not as something to explain away in favor of whichever answer feels better.

## Common mistakes
- Comparing offers on total comp alone, missing that a lower-comp option might dominate once real priorities (team quality, growth, location) are weighted in.
- Treating "stay" as a null default instead of a real option that should be scored on the same criteria as the new offers.

## Example
Input: Offer A has higher comp but the team/manager reads as weaker from real conversations; priorities rank team quality above comp.
Expected output: recommends Offer B despite lower comp, given the stated priority weighting, and states the recommendation would flip if comp were weighted above team quality.

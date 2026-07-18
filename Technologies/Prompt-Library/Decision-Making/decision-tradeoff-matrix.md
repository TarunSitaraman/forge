# Decision Tradeoff Matrix

*Purpose:* Force an explicit, weighted comparison of real options against the criteria that actually matter, instead of a gut-feel decision dressed up after the fact.

## When to use
Any non-trivial decision with 2+ real options, where the "obviously right" answer isn't actually obvious once you try to state why.

## Expected input
The decision, the real options (not strawmen), and the criteria that matter for this specific decision, weighted if some matter more than others.

## Expected output
A scored matrix, a recommendation, and — critically — the condition under which the recommendation would flip.

## The complete prompt
```
Build a decision matrix for this choice.

Decision: {decision}
Real options: {options — must be genuinely viable, not strawmen included
to make one option look better}
Criteria that matter: {criteria, e.g. cost, time, reversibility, team
morale, risk}
Criteria weights (if some matter more): {weights, or state "unweighted"}

For each option × criterion:
1. Score it, with one sentence of justification tied to actual facts
   about the option, not vibes.
2. Flag any criterion where the scoring required an assumption or unknown
   — state exactly what would need to be true/known to score it
   confidently.

Then:
3. Compute the weighted recommendation.
4. State explicitly the condition under which the recommendation would
   flip — which criterion, weighted how differently, would change the
   answer. This is often more useful than the recommendation itself.
5. Name the single biggest risk of the recommended option, even though
   it won and even if that risk is unlikely.
```

## Variants
- **High-stakes/irreversible variant:** add an explicit reversibility-weighted scoring pass — irreversible options get a higher bar.
- **Team-decision variant:** run individually per stakeholder's weights first, then compare where the weightings actually diverge (the real disagreement is often about weights, not facts).

## Related prompts
- [[architecture-decision-record]]
- [[project-scope-cut-plan]]

## Tips
- Insist on real options, not a strawman included to make the preferred choice look better — a matrix that isn't a foregone conclusion is worth doing.
- The "what would flip this" question is often the most valuable output — it turns a decision into something revisitable if circumstances change.

## Common mistakes
- Scoring based on vibes instead of stated facts, making the matrix look rigorous while just formalizing a pre-existing gut call.
- Treating the final score as unconditionally final instead of checking what assumption it depends on.

## Example
Input: decision = "build in-house vs. buy a vendor tool," criteria = cost, time-to-value, control, weighted toward time-to-value.
Expected output: scores each option, computes vendor as the winner given the time-to-value weight, states the recommendation would flip if long-term cost were weighted more heavily than a 2-year horizon.

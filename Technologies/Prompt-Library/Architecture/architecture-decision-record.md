# Architecture Decision Record (ADR) Generator

*Purpose:* Force a real architectural decision through explicit tradeoff analysis and produce a durable ADR, instead of an unrecorded verbal choice.

## When to use
Before committing to a significant technical choice (data store, messaging pattern, service boundary) that will be expensive to reverse.

## Expected input
The decision to be made, the options under consideration, and the constraints that matter (scale, team size, latency, cost, time-to-ship).

## Expected output
A complete ADR: context, options with tradeoffs, decision, consequences — ready to drop into `Technologies/`.

## The complete prompt
```
Generate an Architecture Decision Record for this decision.

Decision to make: {decision}
Options under consideration: {options}
Constraints that matter: {constraints — scale, team size, latency, cost, timeline}
What we already know/have ruled out: {prior context}

Produce an ADR with these sections:
1. Context — the forces at play, stated neutrally, no premature conclusion.
2. Options considered — each option with real tradeoffs (not strawmen).
   Include at least one option that is NOT the one you'll recommend, argued
   fairly.
3. Decision — the option chosen, one paragraph of justification tied
   directly to the stated constraints.
4. Consequences — what this makes easier, what it makes harder, what
   technical debt it knowingly accepts, and the condition under which this
   decision should be revisited.

Be decisive. Do not hedge with "it depends" as the final answer — the
constraints given are enough to choose. If they genuinely aren't, name
exactly what missing information would resolve it.
```

## Variants
- **Reversal-cost variant:** add explicit "cost to reverse this decision in 6 months" scoring per option.
- **Team-alignment variant:** frame consequences section for a non-technical stakeholder audience.

## Related prompts
- [[system-design-interview-style]]
- [[pr-review-senior-engineer]]
- [[decision-tradeoff-matrix]]
- [[data-model-normalization-review]]

## Tips
- Give real constraints, not aspirational ones ("must scale to 10M users" when you have 10K) — the ADR quality depends on honest constraints.
- Store the resulting ADR in `Technologies/` immediately; a decision not written down will be re-litigated later.

## Common mistakes
- Asking for an ADR without constraints, producing a generic pros/cons list disconnected from your actual situation.
- Treating the ADR as permanent — the "when to revisit" clause is required precisely because it isn't.

## Example
Input: decision = "choose between Postgres and DynamoDB for a new service," constraints = "team knows SQL well, access patterns still evolving, launching in 3 weeks."
Expected output: recommends Postgres given evolving access patterns and team familiarity, with DynamoDB fairly argued as better if access patterns were fixed and scale were proven.

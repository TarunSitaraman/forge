# Technical Explainer for Non-Experts

*Purpose:* Explain a technical concept or decision to a non-technical stakeholder accurately, without dumbing it down into something misleading.

## When to use
Explaining an architecture decision, a bug's impact, or a technical tradeoff to a manager, client, or cross-functional partner.

## Expected input
The technical concept/decision, who the audience is and what they actually need to decide or understand, and any analogy domains the audience is already familiar with.

## Expected output
An explanation that is simplified in presentation but not in substance — the audience should be able to make a correct decision based on it.

## The complete prompt
```
Explain this to a non-technical audience, accurately.

Technical concept/decision: {concept}
Audience: {role, what they need to decide or understand, their familiar
domains for analogy — e.g. "understands business finance" or "understands
cooking"}

Constraints:
1. Simplify the presentation (vocabulary, analogy, structure) — not the
   substance. The audience must end up with an accurate mental model, not
   a comforting but wrong one.
2. Use one analogy from their familiar domain, and stress-test it: does
   the analogy break down anywhere in a way that would mislead them about
   the actual tradeoff? Flag it if so.
3. State explicitly what they need to decide/do, and give them enough to
   do it correctly — don't over-explain background they don't need.
4. Name the one thing that's easy to misunderstand about this if
   oversimplified, and address it directly.

Output the explanation, then separately: the analogy's limitation, stated
plainly, so I can address it if asked.
```

## Variants
- **Written variant:** structure as a short doc/email with a one-line takeaway at the top.
- **Verbal/meeting variant:** structure as talking points with the analogy as the opening line.

## Related prompts
- [[dense-prose-tightening]]
- [[presentation-narrative-arc]]

## Tips
- Give the audience's actual decision, not just "explain X" — the explanation should be scoped to what they need to act correctly.
- Always ask for the analogy's failure point; an unexamined analogy is the most common source of stakeholder misunderstanding.

## Common mistakes
- Simplifying away the actual tradeoff (e.g. "it's more secure" without saying what it costs), leading to a decision made on incomplete information.
- Using an analogy that breaks down exactly at the point that mattered for the decision.

## Example
Input: concept = "why we're migrating from a monolith to microservices," audience = "VP of Product, needs to approve a 2-quarter timeline."
Expected output: explains the tradeoff (faster team autonomy vs. short-term velocity cost) using a team-structure analogy, flags where the analogy oversimplifies operational complexity, ends with the explicit ask (approve the timeline).

# Service Boundary Analysis

*Purpose:* Decide where to draw a service/module boundary using coupling and change-frequency evidence, not intuition alone.

## When to use
When deciding whether to split a monolith module into a service, or merge two services back together.

## Expected input
Description of the current module(s)/service(s), what changes together historically, and team ownership structure.

## Expected output
A recommendation on the boundary with explicit coupling evidence, plus the interface contract if a split is recommended.

## The complete prompt
```
Help me decide the right service boundary here.

Current state: {description of module(s)/service(s)}
What changes together historically: {co-change patterns, e.g. "these two
modules are edited in the same PR ~70% of the time"}
Team ownership: {who owns what today}
Data dependencies: {shared tables/data owned across the boundary}

Analyze:
1. Coupling evidence — is this a case of high cohesion (things that change
   together, belong together) or accidental coupling (things that change
   together only because they're forced to share a boundary)?
2. Data ownership — can each side own its data independently, or is there
   a shared source of truth that would need synchronization/duplication?
3. Team fit — does the proposed boundary match how teams actually work, or
   would it force cross-team coordination on every change?
4. Recommendation: keep together / split, with the specific interface
   contract (API shape, events, or shared library) if splitting.
5. The single strongest argument against your own recommendation.
```

## Variants
- **Monolith-to-microservices variant:** add explicit extraction order (which service to peel off first, and why that one is lowest-risk).
- **Merge-back variant:** frame around evidence that a prior split created more coordination overhead than it saved.

## Related prompts
- [[architecture-decision-record]]
- [[system-design-interview-style]]

## Tips
- Co-change frequency (from git history) is the single best signal here — pull it before running this prompt if possible.
- Always ask for the counter-argument step; boundary decisions are easy to rationalize after the fact.

## Common mistakes
- Splitting services along org-chart lines instead of data/coupling lines, creating chatty cross-service calls.
- Ignoring data ownership and ending up with two services fighting over the same source of truth.

## Example
Input: a "users" module and "billing" module that are edited together in 60% of PRs due to a shared `Account` table.
Expected output: recommends against splitting until the `Account` table is decomposed, since the coupling is real (shared data), not accidental.

# Template: Design Decision Record (ADR)

*Guidance: an ADR's value is in recording WHY, including the rejected
alternatives — so the decision doesn't get silently re-litigated by
someone who doesn't know it was already considered. See
`../Prompt-Library/Architecture/architecture-decision-record.md` for
a prompt that generates this from a discussion.*

---

# ADR-{number}: {Decision title}

**Status:** {Proposed / Accepted / Superseded by ADR-{n} / Deprecated}
**Date:** {date}

## Context

{The forces at play — technical, business, team — stated neutrally, no
premature conclusion. What made this decision necessary?}

## Options considered

### Option A: {name}
- **Pros:** {real ones}
- **Cons:** {real ones}

### Option B: {name}
- **Pros:** {real ones}
- **Cons:** {real ones}

*(Include every option that was genuinely considered — a strawman option
included just to make the chosen one look better defeats the purpose.)*

## Decision

{The option chosen, with the justification tied directly to the context
above.}

## Consequences

**This makes easier:** {benefit}
**This makes harder / debt accepted:** {cost, honestly stated}

## Revisit when

{The specific condition — not "never" — under which this decision should
be reconsidered.}

---

## Best practices for this template
- Include at least one seriously-argued rejected option — an ADR with
  only the winning option isn't a decision record, it's a justification
  after the fact.
- State the revisit condition specifically (a metric, a scale threshold,
  a deadline) — "we might reconsider someday" never actually triggers
  a reconsideration.
- Never delete or heavily edit an ADR after acceptance — if the decision
  changes, write a new ADR that supersedes it, preserving the history of
  why the original choice was made.

## Related Templates
- [`decision-log.md`](decision-log.md) — for smaller, lower-stakes decisions that don't warrant a full ADR
- [`feature-proposal.md`](feature-proposal.md)

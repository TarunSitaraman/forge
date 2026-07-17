# Template: Decision Log

*Guidance: a running log of smaller, lower-stakes decisions — distinct
from `design-decision-record.md` (a full ADR for one significant,
hard-to-reverse architectural decision). Use this for the steady stream
of smaller calls a project makes that still deserve a one-line
paper trail, without the overhead of a full ADR per entry.*

---

# Decision Log: {Project Name}

*One row per decision. Promote a decision to a full
`design-decision-record.md` if it turns out to be more significant or
contested than expected.*

| Date | Decision | Why | Made by | Reversible? |
|---|---|---|---|---|
| {date} | {the decision, stated concretely} | {one-line reason} | {name} | {Yes / No / Costly} |

## Best practices for this template
- Keep entries to one line each — if a decision needs more than that to
  justify, it's a candidate for a full `design-decision-record.md`
  instead.
- Log the decision when it's made, not retroactively — the "why" is
  hardest to reconstruct accurately after the fact.
- Note reversibility honestly; it's the fastest way to spot which small
  decisions actually deserved more deliberation than they got.

## Example

| 2026-07-10 | Use UUID v7 instead of v4 for new primary keys | Time-ordered IDs improve index locality for our write pattern | {name} | Costly (would need a migration) |

## Related Templates
- [`design-decision-record.md`](design-decision-record.md) — for a decision significant enough to warrant a full ADR
- [`../Project-System/project-notebook.md`](../Project-System/project-notebook.md) — its Lessons Learned section is the place for what a decision taught you, not just what was decided

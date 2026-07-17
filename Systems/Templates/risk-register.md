# Template: Risk Register

*Guidance: a risk register is a living document, reviewed on a cadence —
not a one-time brainstorm filed away and forgotten. Its value is in
catching a risk's status change (materializing, growing, resolved) while
there's still time to act, not in the initial list itself.*

---

# Risk Register: {Project / System Name}

*Last reviewed: {date}*

## Active risks

| Risk | Likelihood | Impact | Owner | Mitigation | Status |
|---|---|---|---|---|---|
| {risk description} | {Low/Med/High} | {Low/Med/High} | {name} | {specific mitigating action} | {Monitoring/Mitigating/Escalated} |

*(A risk without a specific mitigation is just a worry — every row
should have an actual action, even if that action is "monitor for X
signal.")*

## Materialized risks (became real issues)

{Move a row here once a risk actually happens — this is the evidence for
whether the register is catching real risk or just generating noise.}

| Risk | What happened | How it was handled |
|---|---|---|

## Retired risks (no longer applicable)

{Move a row here once a risk is confirmed to no longer apply — with the
reason, so the register doesn't silently shrink without explanation.}

| Risk | Why retired | Date |
|---|---|---|

## Review cadence

{How often this register is actually reviewed — weekly for a fast-moving
project, monthly for a stable one. State it, and stick to it.}

---

## Example

A row like "Vendor API has no SLA and has had 3 outages in the past
year | High | Med | {name} | Add a circuit breaker + cached fallback;
evaluate a second vendor by Q3 | Mitigating" is concrete enough to act
on. "Third-party risk" with no mitigation and no owner is not.

## Best practices for this template
- Every active risk needs an owner and a concrete mitigation — a risk
  with neither is just an anxiety, not a tracked risk.
- Review on a fixed cadence, not only when something goes wrong — the
  entire value of a register is catching risk before it materializes.
- Use `Systems/Prompt-Library/Decision-Making/premortem-analysis.md` to
  generate the initial risk list with real, specific failure narratives
  rather than generic categories.

## Related Templates
- [`project-plan.md`](project-plan.md)
- [`feature-proposal.md`](feature-proposal.md) — its own Risks section can seed a new register row
- [`retrospective.md`](retrospective.md) — check the register during any recurring retrospective
- [`weekly-review.md`](weekly-review.md) — check the register during any recurring weekly review

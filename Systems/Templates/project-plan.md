# Template: Project Plan

*Guidance: a project plan exists to make dependencies, checkpoints, and
scope-cut decisions visible before they become urgent. If a plan doesn't
let you answer "are we on track" at a glance, it's not doing its job.*

---

# Project Plan: {Project Name}

*Owner: {name}. Start: {date}. Target completion: {date}.*

## Goal

{One sentence: what does success look like for this project?}

## Scope

### Must have
- {Non-negotiable for this project to be considered successful}

### Should have
- {Valuable, but the project succeeds without it}

### Could have / explicitly out of scope
- {Nice-to-have, or deliberately excluded — say which}

## Dependencies

| Depends on | Type (internal/external) | Risk | Mitigation |
|---|---|---|---|
| {what} | {internal team / vendor / API / decision} | {low/med/high} | {plan if it slips} |

## Milestones / checkpoints

| Checkpoint | Target date | What must be true | Scope-cut fallback if missed |
|---|---|---|---|
| {name} | {date} | {concrete, checkable state} | {what gets cut} |

## Team & ownership

| Workstream | Owner | Notes |
|---|---|---|

## Risks

{Top 2-3 risks, ideally from a pre-mortem pass — see
`Systems/Prompt-Library/Decision-Making/premortem-analysis.md`.}

## Status log

*Update this at each checkpoint — don't let it go stale.*

| Date | Status | Notes |
|---|---|---|

---

## Best practices for this template
- Write the scope-cut fallback for each checkpoint NOW, not when the
  checkpoint is actually missed — decisions made under deadline pressure
  are worse than ones made with a clear head.
- Keep the status log current; a plan nobody updates is worse than no
  plan, since it creates false confidence that things are tracked.
- Assign ownership per checkpoint/workstream, not per vague "area" —
  ambiguous ownership is the most common cause of dropped work.

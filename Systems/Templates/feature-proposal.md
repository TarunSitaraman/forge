# Template: Feature Proposal

*Guidance: a feature proposal justifies WHY before describing WHAT. A
proposal that jumps straight to a spec without stating the problem and
who has it invites scope creep and makes it impossible to evaluate
tradeoffs later.*

---

# Feature Proposal: {Feature Name}

**Author:** {name} · **Date:** {date} · **Status:** {Draft / Under review / Approved / Rejected}

## Problem

{Who has this problem, and what does it cost them today (time, money,
frustration, lost opportunity)? Be specific — "users want this" is not a
problem statement.}

## Goal

{What does success look like if this feature exists? State it as an
outcome, not a feature description.}

## Non-goals

{What this proposal explicitly does NOT attempt to solve — prevents
scope creep and sets reviewer expectations.}

## Proposed solution

{The approach, at a level of detail appropriate to the proposal stage —
a one-pager doesn't need full API specs, a build-ready proposal does.}

## Alternatives considered

| Alternative | Why not chosen |
|---|---|
| {option} | {reason} |

## User impact

{Who is affected, positively and negatively. Any migration/breaking
change implications?}

## Success metrics

{How will we know this worked after shipping? Name the actual metric and
the bar for success — not just "usage will go up."}

## Cost / effort estimate

{Rough sizing — team-weeks, or S/M/L/XL if precise estimation isn't
possible yet.}

## Risks

{What could make this fail or backfire — see
`Systems/Prompt-Library/Decision-Making/premortem-analysis.md` for a
structured pass.}

## Open questions

- {Anything that needs an answer before this can be approved/built}

---

## Best practices for this template
- State the problem and who has it before describing the solution —
  proposals that skip this are hard to evaluate and easy to scope-creep.
- Name a real success metric with a bar, not just directional ("more
  engagement") — undefined success criteria make it impossible to know
  if the feature actually worked.
- Keep "alternatives considered" honest — include at least one real
  alternative, not a strawman that makes the proposal look inevitable.

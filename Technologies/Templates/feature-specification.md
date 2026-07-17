# Template: Feature Specification

*Guidance: a specification is the engineering-facing follow-on to an
approved `feature-proposal.md` — it answers HOW and exactly WHAT, in
enough detail to build and test against, once the WHY has already been
settled. Don't use this to re-argue whether the feature should exist;
that argument belongs in the proposal.*

---

# Feature Specification: {Feature Name}

**Status:** {Draft / Ready for build / In progress / Shipped}
**Source proposal:** {link to the approved `feature-proposal.md`, if one exists}

## Summary

{One paragraph: what this feature does, assuming the reader already
knows why it's being built.}

## Functional requirements

{The specific, testable behaviors this feature must exhibit — written
so each one could become a test case.}

- {requirement}

## User flow / interaction

{Step-by-step of how a user (or calling system, for an API-only feature)
actually interacts with this — the sequence of actions and observable
results.}

## Edge cases and error states

{Every non-happy-path case worth specifying explicitly — empty input,
permission denied, concurrent modification, partial failure. A spec that
only covers the happy path leaves these to be improvised during
implementation.}

| Case | Expected behavior |
|---|---|
| {edge case} | {what should happen} |

## Acceptance criteria

{The specific, checkable conditions that mean this feature is done —
each one should be verifiable by a reviewer or a test, not a matter of
opinion.}

- [ ] {criterion}

## Out of scope

{Explicitly excluded from this specification — carried over from the
proposal's non-goals, or narrowed further now that implementation
details are being decided.}

## Dependencies

{What this feature needs from other systems/teams/features before it can
be built or shipped.}

---

## Example

A specification for "CSV export of a report" would state the exact
columns and ordering (functional requirements), what happens when the
report has zero rows or exceeds a size limit (edge cases), and the
specific acceptance criterion "exported file opens correctly in Excel and
Google Sheets" rather than a vague "export works."

## Best practices for this template
- Write requirements so each one is directly testable — if you can't
  imagine the test case, the requirement isn't specific enough yet.
- Don't skip edge cases to save time; unspecified edge cases become
  undocumented implementation decisions made under deadline pressure.
- Keep this a living document during implementation — if reality forces
  a change to a requirement, update the spec rather than letting it
  silently diverge from what's actually built.

## Related Templates
- [`feature-proposal.md`](feature-proposal.md) — the upstream why/should-we-build-this document
- [`design-decision-record.md`](design-decision-record.md) — for a significant technical decision made while implementing this feature
- [`api-documentation.md`](api-documentation.md) — if this feature exposes a new API surface

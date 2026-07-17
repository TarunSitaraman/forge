# Template: Retrospective

*Guidance: a retrospective is for a team/project reflecting together at
a natural end point (sprint end, project end, incident resolved) — it's
distinct from `weekly-review.md` (a personal, ongoing status check) and
from the Project Notebook's own inline Retrospective section
(`../../../Technologies/Project-System/project-notebook.md`, for a single project's
end-of-life reflection). Use this standalone template when a
retrospective needs its own artifact — a team sprint retro, a
post-incident retro — separate from a project's running notebook.*

---

# Retrospective: {Sprint / Project / Event Name}

**Date:** {date} · **Participants:** {names}

## What went well

{Specific, not generic — "the migration finished a day early because we
caught the schema issue in staging," not "good teamwork."}

## What didn't go well

{Equally specific and honest — a retrospective that only lists positives
can't drive any real change.}

## Root causes (for what didn't go well)

{Don't stop at symptoms — use
`../Prompt-Library/Debugging/root-cause-five-whys.md` framing if a
"what didn't go well" item needs deeper investigation than a quick
gut-read explanation.}

## Action items

| Action | Owner | Due | Follow-up check |
|---|---|---|---|

*(An action item with no owner or due date reliably doesn't happen —
see `../Templates/meeting-notes.md` for the same discipline applied
to any meeting.)*

## What we'll keep doing

{Practices worth explicitly continuing — naming these prevents good
habits from eroding silently over time.}

## What we'll stop or change

{Specific practices to stop, distinct from the action items above which
are usually one-time fixes rather than ongoing practice changes.}

---

## Example

"What didn't go well: code review turnaround averaged 2 days, blocking
merges" is specific enough to act on. Its action item might be "set a
team norm of reviewing PRs within 4 business hours," owner assigned, with
a follow-up check at the next retrospective to confirm it actually
changed turnaround time — not just that the norm was announced.

## Best practices for this template
- Be as specific and honest in "what didn't go well" as in "what went
  well" — an unbalanced retrospective doesn't drive real improvement.
- Always assign an owner and due date to action items, and actually
  check them at the NEXT retrospective — an action item log that's
  never revisited trains the team to stop taking retros seriously.
- Distinguish one-time action items from ongoing practice changes
  ("what we'll stop or change") — conflating them makes it hard to know
  what should actually be checked on later.

## Related Templates
- [`weekly-review.md`](weekly-review.md) — the personal, ongoing counterpart to this team/project retrospective
- [`meeting-notes.md`](meeting-notes.md)
- [`risk-register.md`](risk-register.md) — check for materialized risks worth discussing here

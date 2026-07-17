# Project System

*One template every project begins from. The goal is a lightweight
engineering notebook — not a heavyweight process — that keeps a project's
requirements, decisions, risks, and lessons in one place as it evolves,
instead of scattered across chat history and forgotten context.*

## Using it

1. Copy [`project-notebook.md`](project-notebook.md) into
   `Projects/<project-name>/notebook.md` the moment a project starts —
   see `Systems/Playbooks/starting-a-software-project.md` for the full
   kickoff flow this fits into.
2. Fill in Overview, Requirements, and Architecture before writing any
   code — an empty notebook at kickoff is a sign the project isn't
   scoped yet.
3. Update Timeline, Risks, and Lessons Learned continuously, not
   retroactively — their entire value is in being current.
4. Fill in Retrospective, Future Improvements, and Portfolio Entry once
   the project reaches a natural end point or major milestone.

## Why one file, not many

A scattered project (a README here, a risks doc there, decisions in Slack)
makes it hard to answer "where do things stand" at a glance. One notebook
per project, updated in place, keeps that answer to one file — link out
to `Systems/Templates/architecture-document.md`,
`Systems/Templates/project-plan.md`, or a full ADR only when a section
genuinely outgrows a summary.

## Relationship to other Systems modules

- **Kickoff process:** `Systems/Playbooks/starting-a-software-project.md`
- **Fuller architecture doc (if needed):** `Systems/Templates/architecture-document.md`
- **Fuller project plan (if needed):** `Systems/Templates/project-plan.md`
- **Demo scripting:** `Systems/Prompt-Library/Presentations/demo-script-design.md`
- **Portfolio write-up:** `Systems/Templates/portfolio-project.md`
- **Risk analysis:** `Systems/Prompt-Library/Decision-Making/premortem-analysis.md`

The notebook is the hub; these are the spokes you reach for only when a
section needs more depth than a summary.

# Roadmap

Forge grows only when a new capability measurably speeds up execution.
Nothing here is added preemptively.

## Now

- [x] Core structure: `Inbox/`, `Projects/`, `Systems/`, `Reference/`,
      `Archive/`.
- [x] Conventions, workflow, and onboarding documented.
- [x] Elite Prompt Library — 52 operating-procedure-grade prompts across
      22 categories (see `Systems/Prompt-Library/`).
- [x] Playbooks — 15 execution-focused workflows for recurring efforts
      (see `Systems/Playbooks/`).
- [x] Templates — 16 production-quality reusable document templates
      (see `Systems/Templates/`).

## Next

- [ ] Project scaffold template (`Systems/templates/project-readme.md`)
      so starting a new `Projects/` entry takes one command, not a blank
      page.
- [ ] Decision record template standardized across `Systems/`.
- [ ] Minimal Dataview queries (if adopted) for "active projects" and
      "stale Inbox items" — evaluated against the minimal-plugins
      principle before being added.

## Later, if justified

- [ ] Cross-links audit tooling (script, not plugin) to catch orphaned
      files.
- [ ] Archive compaction pass once `Archive/` grows large enough that
      navigation slows down.

## Explicitly out of scope

- Daily journaling of any kind.
- Task/calendar management — Forge is not a planner.
- A general-purpose personal wiki. Every addition must serve execution
  speed, not comprehensiveness.

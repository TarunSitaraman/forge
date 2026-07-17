# Roadmap

Forge grows only when a new capability measurably speeds up execution.
Nothing here is added preemptively.

## Now

- [x] Core structure: `Inbox/`, `Projects/`, `Technologies/`, `Courses/`,
      `Career/`, `Resources/`, `Reference/`, `Archive/`.
- [x] Conventions, workflow, and onboarding documented.
- [x] Elite Prompt Library — 79 operating-procedure-grade prompts across
      28 categories (see `Technologies/Prompt-Library/`).
- [x] Playbooks — 18 execution-focused workflows for recurring efforts
      (see `Technologies/Playbooks/`).
- [x] Templates — 21 production-quality reusable document templates
      (see `Technologies/Templates/`).
- [x] Competitive Programming module — 8 skill-growth trackers + 10
      coaching prompts (see `Courses/Competitive-Programming/`).
- [x] Career module — 10 actionable trackers/checklists + 9 coaching
      prompts (see `Career/`).
- [x] Project System — one engineering-notebook template every project
      scaffolds from (see `Technologies/Project-System/`).
- [x] Docs module — 10 authoritative technology reference manuals (Azure,
      Databricks, Docker, Git, LangChain, RAG, Vector Databases, LLMs,
      Prompt Engineering, Markdown) (see `Technologies/Docs/`).
- [x] Resources module — curated (not exhaustive) external resources
      across 11 categories, each with a stated reason it earns its place
      (see `Resources/`).
- [x] Decision record templates standardized — a full ADR
      (`design-decision-record.md`) plus a lightweight running
      `decision-log.md` for smaller decisions.

## Next

- [ ] Minimal Dataview queries (if adopted) for "active projects" and
      "stale Inbox items" — evaluated against the minimal-plugins
      principle before being added.
- [ ] Backport explicit Prerequisites/Common-mistakes sections into the
      original 15 Playbooks, matching the stronger shape used by
      `deployment.md`, `repository-cleanup.md`, and `prompt-creation.md`.

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

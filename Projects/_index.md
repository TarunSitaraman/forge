# Projects

*Active build work. One folder per project.*

Each project gets its own folder: `Projects/<kebab-case-name>/`, with at
minimum a `_index.md` explaining the project's scope, status, and key links.
Large projects can break content into focused topic documents (e.g., architecture,
deployment, testing).
minimum a `README.md` stating the goal, current status, and links to any
`Technologies/`/`Reference/` material it depends on.

## Active Projects

| Project | Status | Purpose |
|---------|--------|---------|
| **[SmartResQ](./smartresq/)** | Ownership Handoff | Emergency dispatch coordination system. Real-time routing of ambulances + hospital pre-notification from patient SOS/wearables. Production-grade code, infrastructure migration in progress. |

## Project Structure

Each project should have:
- `_index.md` — entry point, navigation hub, key metrics
- Topic documents (architecture, operations, testing, etc.) — optimized for search and reading
- `INTERVIEW_GUIDE.md` (optional) — reference material for ownership transfer or team onboarding

## Rules

**Active vs. Archive:** If not actively executing, move to `Archive/` within 2–3 weeks (`git mv Projects/<name> Archive/<name>`). Revive by moving back.

**Reusable content:** Patterns or decisions that outlive one project go in `Systems/` and get linked from here (don't duplicate).

**Cross-linking:** Link related `Systems/` content (playbooks, templates, reference docs) from project docs. This improves discoverability.
Does not belong here: reusable patterns or decisions that outlive this
one project — those go in `Technologies/` and get linked from here instead of
duplicated.

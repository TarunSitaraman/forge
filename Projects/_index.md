# Projects

*Active build work. One folder per project.*

Each project gets its own folder: `Projects/<kebab-case-name>/`, with at
minimum a `_index.md` explaining the project's scope, status, and key
links. Larger projects break content into focused topic documents
(architecture, deployment, testing, etc.) rather than one long file.

## Active Projects

| Project | Status | Purpose |
|---------|--------|---------|
| **[SmartResQ](./smartresq/)** | Ownership Handoff | Emergency dispatch coordination system. Real-time routing of ambulances + hospital pre-notification from patient SOS/wearables. Production-grade code, infrastructure migration in progress. |
| **[Personal Agent](./personal-agent/)** | Deployed, Active | WhatsApp-native personal AI agent ("Blu") — context-aware second brain with a multi-tier LLM stack, Postgres + pgvector memory, and proactive scheduled briefs/nudges. |
| **[QuickCover](./quickcover/)** | Hackathon Complete | AI-powered parametric income protection for Q-commerce gig workers — consumer-funded micro-surcharge, automatic weather/outage-triggered payouts, XGBoost pricing + Isolation Forest fraud + GenAI adjudication. |

## Project Structure

Each project should have:
- `_index.md` — entry point, navigation hub, key metrics
- Topic documents (architecture, operations, testing, etc.) — optimized
  for search and reading, numbered if the project is large enough to
  warrant it (see `smartresq/` or `personal-agent/` for the pattern)
- `INTERVIEW_GUIDE.md` (optional) — reference material for ownership
  transfer, team onboarding, or interview prep once the project has
  enough real mileage to draw concrete stories from

## Rules

**Active vs. Archive:** If not actively executing, move to `Archive/`
within 2-3 weeks (`git mv Projects/<name> Archive/<name>`). Revive by
moving back.

**Reusable content:** Patterns or decisions that outlive one project
belong in `Technologies/` and get linked from here — don't duplicate
them into a project's own docs.

**Cross-linking:** Link related `Technologies/` content (playbooks,
templates, reference docs) from project docs. This improves
discoverability.

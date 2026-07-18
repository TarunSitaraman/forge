# Git Workflow

Forge is Git-first: the commit history is the audit trail of your
thinking and building, not a note revision log.

## Branching

- `main` is always in a clean, navigable state.
- For substantial reorganizations or multi-file additions (a new Technologies
  entry, a Prompt Library category, a project scaffold), branch:
  `git checkout -b <kebab-case-change-name>`.
- For quick single-file capture or edits during a build session,
  committing directly to `main` is fine — Forge optimizes for speed, not
  ceremony.

## Commits

- Commit at natural checkpoints: end of a capture session, after filing
  the Inbox, after finishing a project doc — not after every keystroke,
  not once a week.
- Commit messages are imperative and specific: `Add gRPC vs REST decision
  record`, not `updates`.
- One logical change per commit. Filing ten Inbox items into their
  homes is one commit; starting a new project is a separate one.

## Reviewing

- Solo use: push straight to `main` after a self-review of the diff
  (`git diff --staged`).
- Collaborative use: open a PR for anything touching `Technologies/`, `Career/`,
  `Courses/`, `Resources/`, or `Reference/`, since those are shared, reusable
  material other people's work will depend on. `Projects/` and `Inbox/` don't
  need review — they're yours to move fast in.

## Weekly maintenance

1. `Inbox/` should be empty or near-empty. Anything older than a week
   gets filed or deleted.
2. Finished projects move from `Projects/` to `Archive/` in their own
   commit: `git mv Projects/<name> Archive/<name>`.
3. Dead links checked (Obsidian's "Unresolved links" panel) and fixed
   before they accumulate.

## What not to do

- Don't force-push shared branches.
- Don't squash away the history of a `Technologies/` decision record — the
  evolution of a decision is often as valuable as the decision itself.
- Don't let `Inbox/` become a second `main` branch — it's a queue, not
  storage.

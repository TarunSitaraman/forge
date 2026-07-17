# Forge

**A Git-first, Obsidian-compatible engineering workbench.**

Forge is not a notes vault. It is not a journal. It is not a place to dump
half-formed thoughts and hope they compost into something useful later.

Forge exists for one reason: **to make you build things faster.**

Every file in this repository should either help you execute right now, or
capture something reusable so you execute faster next time. If a file does
neither, it doesn't belong here.

---

## What Forge is

- **Git-first.** The repository is the source of truth. History,
  branches, and diffs are how Forge tracks change — not backlinks, not
  plugin metadata, not a database.
- **Obsidian-compatible.** Open the repo as an Obsidian vault and get
  graph view, quick switcher, and backlinks for free. Obsidian is a
  *reader/editor* for Forge, not a dependency. Every file must remain
  fully useful as plain Markdown in any editor, GitHub's file viewer, or
  `grep`.
- **Markdown only.** No databases, no proprietary formats, no plugin
  lock-in for content. Plugins may change; the Markdown must not need
  them to be readable.
- **Minimal plugins.** See [`.obsidian-config/`](.obsidian-config) for the
  short, deliberate list. Every plugin is a liability — it must earn its
  place by measurably speeding up navigation or capture.
- **Fast navigation.** A five-folder root, consistent naming, and no
  nesting deeper than necessary. You should never need to think about
  *where* something lives.

## What Forge is not

- Not a daily journal.
- Not a place for random note dumping.
- Not a personal wiki of every thought you've ever had.
- Not a task manager or calendar.
- Not a place where files accumulate without a reason to exist.

If you want to write freely, write elsewhere. Forge is where finished
thinking becomes reusable engineering material.

---

## Repository structure

```
forge/
├── README.md            You are here.
├── START_HERE.md         Onboarding + daily entry point.
├── CONVENTIONS.md         Naming, Markdown, and tagging rules.
├── WORKFLOW.md            Git workflow for using Forge day to day.
├── ROADMAP.md             Where Forge is headed.
├── .obsidian-config/      Minimal, version-controlled Obsidian setup.
├── Inbox/                 Unsorted capture. Must be emptied on a cadence.
├── Projects/              Active build work. One folder per project.
├── Systems/               Reusable engineering systems: patterns,
│                          architectures, decisions, checklists.
│   ├── Prompt-Library/    Elite, operating-procedure-grade AI prompts.
│   ├── Playbooks/         Execution workflows for recurring efforts.
│   └── Templates/         Production-quality reusable document templates.
├── Reference/             Durable technical reference material.
└── Archive/               Completed or dead projects, kept for record.
```

Each folder contains an `_index.md` explaining its purpose, scope, and
what does *not* belong there. Read `_index.md` before adding a file to
any folder you're unfamiliar with.

## Getting started

New to Forge? Read [`START_HERE.md`](START_HERE.md).

Adding content? Read [`CONVENTIONS.md`](CONVENTIONS.md) first — naming and
structure are enforced by discipline, not tooling, so consistency is on
you.

Committing changes? Read [`WORKFLOW.md`](WORKFLOW.md).

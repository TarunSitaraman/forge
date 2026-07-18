# Conventions

Consistency here is what keeps navigation fast. These rules are enforced
by discipline, not tooling — follow them even when no one's checking.

## Naming conventions

- **Files:** `kebab-case.md` — `service-mesh-migration.md`, not
  `Service Mesh Migration.md`. Lowercase, hyphen-separated, no dates in
  filenames unless the file *is* dated content (e.g. a changelog entry).
- **Folders:** `PascalCase` for top-level structural folders (`Projects`,
  `Technologies`, `Courses`, `Career`, `Resources`, `Reference`, `Archive`), `kebab-case` for everything created inside them (project folders, technology folders).
- **Project folders:** `Projects/<kebab-case-project-name>/`. Each
  project folder contains at minimum a `README.md` stating goal, status,
  and links to related `Technologies/`, `Career/`, `Courses/`, or `Reference/` material.
- **No version suffixes.** No `-v2`, `-final`, `-old` in filenames — Git
  history is the version control. If a file is superseded, replace it or
  move the old one to `Archive/`.

## Markdown conventions

- One `#` H1 per file, matching the file's purpose — used as the title
  everywhere (Obsidian, GitHub, quick switcher).
- Every file opens with a one-line purpose statement directly under the
  H1, in italics: *"Decision record for adopting gRPC over REST."*
- Use `##`/`###` for structure; don't skip heading levels.
- Prefer tables and checklists over long prose. This is a workbench, not
  an essay collection.
- Code blocks always carry a language hint.
- Internal links use Obsidian wikilinks (`[[other-file]]`) — they render
  correctly in Obsidian and degrade gracefully to visible text elsewhere.
  Use relative Markdown links (`[text](../path.md)`) only when linking
  across the repo in a way that must also work on GitHub's web viewer.

## Tagging conventions

Tags are for cross-cutting retrieval that folders can't express — not a
second filing system. Keep the tag vocabulary small and stable.

- **Namespace every tag:** `#status/...`, `#type/...`, `#stack/...`.
  Never bare tags like `#important`.
- `#status/active`, `#status/blocked`, `#status/done` — project state,
  used only in `Projects/`.
- `#type/pattern`, `#type/decision`, `#type/checklist` — content shape,
  used in `Technologies/`, `Career/`, `Courses/`, and `Resources/`.
- `#stack/<technology>` — e.g. `#stack/kubernetes`, `#stack/postgres` —
  applied anywhere a file is stack-specific, to find everything touching
  a technology regardless of folder.
- Maximum 3 tags per file. If you need more, the file is trying to be
  two files.

## Frontmatter

Minimal YAML frontmatter, only when it carries real metadata:

```yaml
---
status: active
tags: [stack/kubernetes, type/decision]
created: 2026-07-17
---
```

Don't add frontmatter fields "for later." Add them when something
actually reads or filters on them.

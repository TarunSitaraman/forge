---
type: standard
status: stable
tags: [forge, standards, dsa]
canonical: true
---
# Documentation Standards

## YAML Frontmatter Standard
Use frontmatter on every knowledge page with `type`, `status`, `tags`, `canonical`, `created`, `updated`, and `related` when applicable.

## Naming Conventions
Use Title Case for page names and singular nouns for canonical concepts. Problem pages may include the platform prefix when useful.

## File Naming
Prefer readable Markdown filenames such as `Binary Search.md`. Avoid abbreviations unless they are standard, such as `DFS` or `BFS`.

## Tags
Use lowercase tags grouped by scope: `dsa/pattern`, `dsa/algorithm`, `dsa/template`, `dsa/problem`, `dsa/mistake`, `dsa/interview`.

## Cross-Linking Rules
Use Obsidian wiki links for internal references, for example [[Binary Search]], [[Pattern Index]], and [[Problem Index]]. Link to the canonical page, not duplicate notes.

## Canonical Source Rules
Every concept has exactly one canonical home. Other pages summarize briefly and link to the canonical source.

## Folder Responsibilities
Patterns live in [[Pattern Index]], algorithms in [[Algorithm Index]], data structures in [[Data Structure Index]], problems in [[Problem Index]], and reusable code in [[Template Index]].

## Linking Philosophy
Links should explain relationships: uses, depends on, contrasts with, common mistake, representative problem, or template implementation.

## Dataview Compatibility
Keep metadata fields simple scalars or lists. Use stable `type` values and consistent tags for future Dataview queries.

## Examples
```yaml
---
type: pattern
status: draft
tags: [dsa/pattern, dsa/binary-search]
canonical: true
related: [[Binary Search]], [[Wrong Mid Calculation]]
---
```


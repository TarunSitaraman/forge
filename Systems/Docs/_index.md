# Docs

*Engineering reference manuals — not notes. Each technology gets exactly
ONE comprehensive, authoritative Markdown file. When you learn something
new about a technology already covered here, it gets merged into the
relevant section of that file — it never becomes a second file, a dated
note, or an appendix.*

## Index

| Technology | File |
|---|---|
| Azure | [`azure.md`](azure.md) |
| Databricks | [`databricks.md`](databricks.md) |
| Docker | [`docker.md`](docker.md) |
| Git | [`git.md`](git.md) |
| LangChain | [`langchain.md`](langchain.md) |
| RAG | [`rag.md`](rag.md) |
| Vector Databases | [`vector-databases.md`](vector-databases.md) |

## Structure every doc follows

Overview → Mental Model → Architecture (with Mermaid diagrams) → Common
Workflows → Common Mistakes → Best Practices → Cheatsheet → Interview
Questions → Useful Links → Further Reading. Skim any doc top to bottom
to go from zero to operational in one pass.

## Why "Mental Model" is its own section

Overview says what a technology does; Mental Model says how to *think*
about it correctly — the one framing that makes the rest of the
technology's behavior predictable instead of memorized case by case
(e.g. "a Docker image is a stack of layers," "a resource group is not a
network boundary"). This is usually the highest-value section when
you're actually stuck.

## Adding a new technology doc

Only add one for a technology you use often enough that a single
reference page earns its place. Follow the exact section structure of an
existing file. If you're tempted to create a second file for the same
technology (a "gotchas" file, a "notes" file), that content belongs
merged into the existing file's Common Mistakes or Best Practices
section instead.

## Relationship to other Systems modules

- `Systems/Prompt-Library/` holds prompts that *use* this knowledge
  (e.g. `RAG/rag-architecture-review.md` diagnoses problems; this doc
  explains the underlying model).
- `Systems/Resources/` holds curated external resources (docs sites,
  courses, papers) for going deeper than this reference goes.

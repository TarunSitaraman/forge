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
| AI Agents (Agentic AI) | [`ai-agents.md`](ai-agents.md) |
| Vector Databases | [`vector-databases.md`](vector-databases.md) |
| LLMs | [`llms.md`](llms.md) |
| Prompt Engineering | [`prompt-engineering.md`](prompt-engineering.md) |
| Markdown | [`markdown.md`](markdown.md) |
| PostgreSQL | [`postgresql.md`](postgresql.md) |
| Redis | [`redis.md`](redis.md) |
| Kubernetes | [`kubernetes.md`](kubernetes.md) |
| Node.js & Express | [`nodejs-express.md`](nodejs-express.md) |
| React | [`react.md`](react.md) |
| Supabase | [`supabase.md`](supabase.md) |
| FastAPI | [`fastapi.md`](fastapi.md) |

## Structure every doc follows

Overview → Mental Model → Architecture (with Mermaid diagrams) → Core
Concepts → Common Workflows → Common Mistakes → Best Practices →
Cheatsheet → Interview Questions → Useful Links → Further Reading. Skim
any doc top to bottom to go from zero to operational in one pass.

*(The first seven docs — Azure through Vector Databases — fold Core
Concepts into their Mental Model and Architecture sections rather than
breaking it out separately; LLMs, Prompt Engineering, and Markdown use
an explicit Core Concepts section because each covers several
genuinely distinct concepts that don't reduce to one mental-model
framing. Both are acceptable; use an explicit Core Concepts section
when a topic doesn't collapse into a single framing.)*

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

- `../Prompt-Library/` holds prompts that *use* this knowledge
  (e.g. `RAG/rag-architecture-review.md` diagnoses problems; this doc
  explains the underlying model).
- `../../Resources/` holds curated external resources (docs sites,
  courses, papers) for going deeper than this reference goes.

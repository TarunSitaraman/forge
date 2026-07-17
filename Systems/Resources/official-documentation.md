# Official Documentation

*The first place to check, always — before a blog post, before a video,
before asking an LLM that might be working from stale training data on a
fast-moving API. Primary sources are the one category in this module
safe to seed directly, since they're maintained by the tool's own
creators and don't go stale the way a third-party tutorial does.*

---

## Why this category is different from the rest

Every other page in `Systems/Resources/` asks you to personally validate
an entry before adding it, because quality is subjective and volatile.
Official documentation doesn't have that problem in the same way — the
question isn't "is this good" but "is this the actual primary source,"
which is verifiable and stable. That's why this page can be filled in
directly rather than left as an empty template.

## Entries

| Technology | Link | Why it's the first stop |
|---|---|---|
| Docker | [docs.docker.com](https://docs.docker.com/) | Canonical reference for Dockerfile/Compose syntax — third-party tutorials frequently lag behind current best practices (e.g. BuildKit features) |
| Git | [git-scm.com/doc](https://git-scm.com/doc) | Includes the free Pro Git book — deeper and more accurate than most paid Git courses |
| Azure | [learn.microsoft.com/azure](https://learn.microsoft.com/en-us/azure/) | The Architecture Center specifically is a better source for design tradeoffs than most third-party Azure blogs |
| Databricks | [docs.databricks.com](https://docs.databricks.com/) | Databricks-specific behavior (Unity Catalog, DBR version differences) is often wrong or outdated in general Spark tutorials |
| Delta Lake | [docs.delta.io](https://docs.delta.io/) | Authoritative on transaction log semantics that most blog posts simplify incorrectly |
| Apache Spark | [spark.apache.org/docs](https://spark.apache.org/docs/latest/) | The configuration reference is the actual source of truth for tuning parameters |
| LangChain | [python.langchain.com/docs](https://python.langchain.com/docs/) | The framework's API has changed significantly across versions — third-party tutorials are the most common source of version-mismatch confusion here |
| pgvector | [github.com/pgvector/pgvector](https://github.com/pgvector/pgvector) | The README is the primary source for index type tradeoffs (HNSW vs. IVFFlat) |

*(Add a new row only when you've actually used a technology enough that
"where do I check first" has come up — not preemptively for every tool
you might someday touch.)*

## How to use this page

When stuck on something Forge's own `Systems/Docs/` doesn't cover in
enough depth, check here before searching generally — official docs are
usually faster to get a definitive answer from than a search that
surfaces outdated Stack Overflow answers or blog posts written against
an old version.

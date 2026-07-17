# Vector Database Selection

*Purpose:* Choose a vector database based on actual scale, latency, and operational constraints instead of defaulting to whatever's trending.

## When to use
When starting a new RAG/semantic-search project, or when an existing vector store is hitting scale or cost limits.

## Expected input
Corpus size (current and projected), query volume/latency requirement, filtering needs (metadata filters alongside vector search), and operational constraints (self-hosted vs. managed, existing infra).

## Expected output
A specific recommendation with reasoning tied to the stated constraints, not a generic feature comparison table.

## The complete prompt
```
Recommend a vector database for this use case, reasoned from the actual
constraints — not a generic comparison.

Corpus size: {current, projected in 12 months}
Query volume/latency requirement: {QPS, p99 latency target}
Filtering needs: {metadata filters required alongside vector search, and
their selectivity}
Operational constraints: {self-hosted vs. managed preference, existing
infra e.g. "already running Postgres"}
Team's ops capacity: {can they operate a new stateful service, or need
managed}

Reason through:
1. Does the scale genuinely require a dedicated vector database, or would
   pgvector/an extension to existing infra suffice? Say so if it's the
   latter — don't default to a dedicated vector DB out of trend.
2. Given the filtering needs, which candidates support efficient
   pre-filtering (not post-filter-then-discard) at this selectivity?
3. Given latency/QPS targets, which candidates' indexing approach (HNSW,
   IVF, etc.) actually meets it at this corpus size — not just "handles
   billions of vectors" marketing claims.
4. Given ops capacity, is a managed service justified, or is self-hosting
   viable given existing infra investment?
5. Final recommendation, with the specific runner-up and the condition
   under which you'd switch to it instead.
```

## Variants
- **Existing-Postgres-shop variant:** bias explicitly toward pgvector/pgvecto.rs unless scale clearly exceeds what it handles well.
- **Multi-tenant variant:** add explicit tenant-isolation (namespace/partition) support as a hard filter on candidates.

## Related prompts
- [[chunking-strategy-design]]
- [[rag-architecture-review]]
- [[architecture-decision-record]]
- [[embedding-model-evaluation]]

## Tips
- State real corpus size and QPS — vector DB tradeoffs only become meaningful at specific scale thresholds, not in the abstract.
- Always ask "do we even need a dedicated vector DB" first; many projects overprovision here.

## Common mistakes
- Choosing a vector database based on benchmark marketing rather than actual filtering and latency needs.
- Ignoring operational cost of running a new stateful service when the team already operates Postgres well.

## Example
Input: 200K documents, 50 QPS, needs metadata filtering by tenant, team already runs Postgres.
Expected output: recommends pgvector given the modest scale and existing Postgres operational maturity, with Qdrant/Weaviate as the runner-up if scale grows past ~10M vectors.

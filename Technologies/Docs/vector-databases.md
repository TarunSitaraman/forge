# Vector Databases

*One authoritative reference. This is not a note collection — new
learnings get merged into the relevant section below, not appended as a
new file.*

## Overview

A vector database stores high-dimensional embeddings and answers nearest-
neighbor similarity queries efficiently at scale — the retrieval backend
underneath RAG and semantic search. What distinguishes it from a plain
in-memory similarity scan is an **approximate nearest neighbor (ANN)
index**, which trades a small amount of recall for orders-of-magnitude
faster search as the corpus grows into the millions of vectors.

## Mental model

Exact nearest-neighbor search means comparing a query vector against
every stored vector — O(n) per query, fine for thousands of vectors,
untenable for millions. An ANN index (commonly **HNSW** — a navigable
small-world graph — or **IVF** — inverted file, clustering vectors and
searching only relevant clusters) restructures the vector space at
index-build time so a query only needs to examine a small fraction of
vectors to find near-neighbors with high (not perfect) probability of
finding the true nearest ones.

The core tradeoff to internalize: **recall vs. latency vs. index build/
memory cost**. Every ANN index has tunable parameters trading these
three against each other — there's no free lunch, only where you want
the tradeoff to sit for your corpus size and query volume.

## Architecture

```mermaid
flowchart TD
    subgraph Index build (offline)
        VECS[Embedding Vectors] --> IDX[ANN Index: HNSW/IVF]
    end
    subgraph Query time
        Q[Query Vector] --> SEARCH[ANN Search]
        IDX --> SEARCH
        SEARCH --> CAND[Candidate Nearest Neighbors]
        CAND --> FILTER[Metadata Filter]
        FILTER --> RESULT[Top-k Results]
    end
```

**Pre-filter vs. post-filter (a common source of bad results):**
```mermaid
flowchart LR
    subgraph Post-filter (weaker)
        Q1[Query] --> S1[ANN search: top-k]
        S1 --> F1[Filter by metadata]
        F1 --> R1[Fewer than k results if filter is selective]
    end
    subgraph Pre-filter (stronger)
        Q2[Query] --> F2[Filter by metadata first]
        F2 --> S2[ANN search within filtered subset]
        S2 --> R2[Full k results]
    end
```

## Common workflows

**Basic upsert and query (conceptual, API varies by product)**
```python
collection.upsert(
    ids=["doc1_chunk3"],
    vectors=[embedding],
    metadata={"source": "doc1", "section": "3", "date": "2026-07-01"}
)

results = collection.query(
    vector=query_embedding,
    top_k=5,
    filter={"date": {"$gte": "2026-01-01"}}
)
```

**Choosing a distance metric**
```python
# Cosine similarity: most common for text embeddings (magnitude-invariant)
# Dot product: equivalent to cosine if vectors are normalized, faster to compute
# Euclidean (L2): common for image/audio embeddings, magnitude-sensitive
```

**Using pgvector (Postgres extension) for smaller-scale needs**
```sql
CREATE EXTENSION IF NOT EXISTS vector;
CREATE TABLE docs (id bigint, embedding vector(1536), content text);
CREATE INDEX ON docs USING hnsw (embedding vector_cosine_ops);
SELECT id, content FROM docs ORDER BY embedding <=> '[...]' LIMIT 5;
```

## Common mistakes

- **Defaulting to a dedicated vector database when scale doesn't
  justify it.** At hundreds of thousands of vectors with moderate QPS,
  pgvector on existing Postgres infra is often simpler and cheaper to
  operate than a new stateful service.
- **Post-filtering instead of pre-filtering** on metadata — running ANN
  search first and filtering results after can return fewer than top-k
  results (or none) when the filter is selective, since the ANN search
  never considered the filter.
- **Ignoring dimensionality's cost impact.** Higher-dimensional
  embeddings cost more in index memory and search latency; not every use
  case needs the largest available embedding model.
- **Not re-normalizing vectors when required by the chosen distance
  metric** — dot product similarity assumes normalized vectors; skipping
  normalization silently produces wrong rankings.
- **Choosing ANN index parameters (e.g. HNSW's `ef_construction`/`M`)
  without testing recall against a labeled eval set** — tuning purely for
  speed can quietly tank recall below what the application needs.
- **No tenant/namespace isolation in a multi-tenant setup**, risking a
  query returning another tenant's data if filtering isn't enforced at
  the index level, not just the application layer.
- **Treating vector search as a complete solution** for retrieval quality
  problems that are actually chunking or embedding-model-fit issues (see
  `../Docs/rag.md`) — the vector database can only return what was
  indexed well.

## Best practices

- Start with the simplest infra that meets scale needs — pgvector on
  existing Postgres before a dedicated vector database, unless corpus
  size/QPS/filtering needs clearly exceed what it handles well.
- Prefer pre-filtering (filter before or during ANN search) over post-
  filtering whenever the product supports it, especially for selective
  filters.
- Match the distance metric to the embedding model's training
  assumptions (most text embedding models are trained/evaluated for
  cosine similarity).
- Build a small labeled eval set (query → correct document) and measure
  recall@k before tuning ANN parameters for latency.
- Enforce tenant isolation at the index/namespace level in multi-tenant
  systems, not only via application-level query construction.
- Re-evaluate embedding model and index choice as corpus size and query
  patterns evolve — a choice fine at 100K vectors may need revisiting at
  10M.

## Cheatsheet

| Concept | Notes |
|---|---|
| HNSW | Graph-based ANN index; strong recall/latency tradeoff, more memory |
| IVF | Cluster-based ANN index; faster to build, needs tuning of cluster count |
| Cosine similarity | Magnitude-invariant; standard for most text embeddings |
| Dot product | Equivalent to cosine if vectors normalized; slightly cheaper to compute |
| Euclidean (L2) | Magnitude-sensitive; common for image/audio embeddings |
| Pre-filter | Apply metadata filter before/during ANN search — preserves top-k count |
| Post-filter | Apply metadata filter after ANN search — can return fewer than top-k |
| recall@k | Fraction of queries where the true relevant document appears in top-k results |

## Interview questions

1. Why use an ANN index instead of exact nearest-neighbor search?
   *(Exact search is O(n) per query — infeasible at millions of vectors
   with real-time latency needs; ANN indexes trade a small amount of
   recall for major speed gains via structures like HNSW or IVF.)*
2. What's the difference between pre-filtering and post-filtering, and
   why does it matter? *(Pre-filtering restricts the search space before
   or during the ANN search; post-filtering searches everything then
   discards non-matching results after — post-filtering with a selective
   filter can return far fewer than the requested top-k results.)*
3. When would pgvector be preferable to a dedicated vector database?
   *(Moderate scale, existing Postgres operational investment, and no
   need for capabilities a dedicated vector DB uniquely provides at that
   scale — avoids the operational cost of a new stateful service.)*
4. How would you choose a distance metric for a given embedding model?
   *(Match what the embedding model was trained/evaluated with — usually
   cosine similarity for text embeddings; using an inconsistent metric
   can silently degrade ranking quality.)*
5. How do you validate that ANN index tuning (e.g. HNSW parameters)
   hasn't hurt recall too much for the sake of speed? *(Measure recall@k
   against a labeled eval set before and after tuning — don't rely on
   "it feels faster" without checking that relevant results are still
   being returned.)*

## Useful links

- [pgvector](https://github.com/pgvector/pgvector)
- [HNSW paper (Malkov & Yashunin)](https://arxiv.org/abs/1603.09320)
- General ANN benchmarking resource: [ann-benchmarks.com](https://ann-benchmarks.com/)

## Further reading

- `../Docs/rag.md` for how vector search fits into the broader
  retrieval pipeline.
- `../Prompt-Library/Vector-Databases/vector-db-selection.md` and
  `embedding-model-evaluation.md` for the decision prompts that
  operationalize the tradeoffs above.

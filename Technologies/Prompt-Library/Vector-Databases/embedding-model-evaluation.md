# Embedding Model Evaluation

*Purpose:* Choose or validate an embedding model against your actual domain and query patterns, instead of assuming the highest-ranked MTEB model is automatically best for your data.

## When to use
When selecting an embedding model for a new semantic search/RAG system, or diagnosing whether embeddings are the cause of poor retrieval.

## Expected input
Domain description (general text, code, legal, medical, multilingual), sample query-document pairs that should match, and the candidate model(s) under consideration.

## Expected output
An evaluation plan and a recommendation with the specific domain-fit reasoning, not just a leaderboard citation.

## The complete prompt
```
Evaluate embedding model fit for this domain — don't just cite a leaderboard.

Domain: {general text / code / legal / medical / multilingual / other
specialized}
Sample query-document pairs that SHOULD match: {examples}
Candidate models: {models under consideration}
Constraints: {latency, cost per embedding, self-hosted vs. API,
dimensionality limits from vector DB}

Assess:
1. Domain fit — was each candidate model trained/fine-tuned on data
   resembling this domain (e.g. a general text embedding model on legal
   text may miss domain-specific similarity)? Flag domain mismatch risk
   explicitly.
2. Using the sample query-document pairs, reason through whether each
   candidate would plausibly rank the correct document highly — check
   for domain-specific vocabulary the model might not represent well.
3. Practical constraints — dimensionality vs. vector DB index cost,
   latency/cost tradeoff for self-hosted vs. API-based embedding.
4. Recommend a real evaluation method beyond this reasoning pass: a small
   labeled eval set with recall@k, before committing at scale.
5. Final recommendation with the specific tradeoff you're accepting.
```

## Variants
- **Multilingual variant:** add explicit cross-lingual retrieval test cases if queries and documents may be in different languages.
- **Fine-tuning variant:** add assessment of whether domain-specific fine-tuning of embeddings is justified given eval gap size.

## Related prompts
- [[vector-db-selection]]
- [[chunking-strategy-design]]

## Tips
- Bring real query-document pairs from your domain, especially ones with domain-specific vocabulary a general model might miss.
- Don't skip building a small labeled eval set — reasoning-based assessment is a starting filter, not a substitute for measurement.

## Common mistakes
- Picking the top MTEB-leaderboard model without checking domain fit for specialized text (legal, medical, code).
- Never re-evaluating the embedding model after the corpus or query patterns have shifted significantly since initial selection.

## Example
Input: domain = legal contracts, candidate = a general-purpose text embedding model, sample pairs use legal terms of art.
Expected output: flags risk that general embeddings under-differentiate legal terms of art, recommends testing a legal-domain or fine-tuned model against the sample pairs before commitment.

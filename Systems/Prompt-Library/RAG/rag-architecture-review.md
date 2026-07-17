# RAG Architecture Review

*Purpose:* Diagnose why a RAG system's answers are bad by isolating whether the failure is in retrieval, chunking, or generation — instead of tuning the prompt when the real problem is retrieval.

## When to use
When a RAG system's outputs are unsatisfactory and it's unclear whether the fix belongs in retrieval or generation.

## Expected input
The pipeline description (chunking strategy, embedding model, retrieval method, reranking if any), a few concrete examples of bad outputs with the query and what was retrieved.

## Expected output
A diagnosis of which stage is failing, with evidence, and the specific fix for that stage — not a prompt tweak applied to a retrieval problem.

## The complete prompt
```
Diagnose this RAG system's failure using the concrete examples given.

Pipeline: chunking = {strategy, size, overlap}, embedding model = {model},
retrieval = {top-k, similarity metric}, reranking = {yes/no, method},
generation prompt = {prompt}

Failing examples: for each, give {query}, {chunks actually retrieved},
{answer generated}, {what the correct answer should have been}

For each example, diagnose which stage failed:
1. Retrieval failure — the correct chunk exists in the corpus but wasn't
   retrieved (check: would a different top-k, hybrid search, or query
   rewriting have surfaced it?).
2. Chunking failure — the correct information was split across chunk
   boundaries or buried in a chunk with unrelated content.
3. Generation failure — the correct chunk WAS retrieved, but the model
   ignored it, misread it, or hallucinated on top of it.
4. Corpus gap — the correct information isn't in the corpus at all.

Tally which failure mode dominates across the examples, and recommend the
fix for the dominant one specifically — don't recommend prompt engineering
if the dominant failure is retrieval.
```

## Variants
- **Query-rewriting variant:** add explicit test of query rewriting/expansion as a retrieval fix before recommending embedding model changes.
- **Multi-hop variant:** for questions requiring combining multiple chunks, add explicit check of whether single-pass retrieval is structurally insufficient.

## Related prompts
- [[vector-db-selection]]
- [[chunking-strategy-design]]
- [[hallucination-mitigation-review]]

## Tips
- Always include what was actually retrieved for each bad example — diagnosing without seeing retrieved chunks is guessing.
- Look for a dominant failure mode across several examples before changing anything; one-off failures shouldn't drive architecture changes.

## Common mistakes
- Rewriting the generation prompt repeatedly when the retrieved chunks never contained the right information to begin with.
- Increasing top-k as a blanket fix without checking whether the added chunks are relevant or just dilute the context.

## Example
Input: 5 bad-answer examples, in 4 of them the correct chunk wasn't in the retrieved set at all.
Expected output: diagnoses retrieval failure as dominant, recommends testing hybrid (keyword + vector) search or query rewriting before touching the generation prompt.

# Chunking Strategy Design

*Purpose:* Choose a chunking strategy matched to the actual document structure and query patterns, instead of a default fixed-size chunker that fragments meaning.

## When to use
When designing a new RAG corpus ingestion pipeline, or when chunking is suspected as a cause of poor retrieval.

## Expected input
Sample documents (representative of the corpus), typical queries the system needs to answer, and the current chunking approach if one exists.

## Expected output
A recommended chunking strategy (method, size, overlap, metadata) with reasoning tied to the actual document structure.

## The complete prompt
```
Design a chunking strategy for this corpus, based on its actual structure.

Sample documents: {representative samples — show real structure: headings,
tables, code blocks, lists}
Typical queries: {what users actually ask}
Current approach (if any): {method, chunk size, overlap}

Analyze:
1. Document structure — do documents have natural semantic boundaries
   (headings, sections) that a fixed-size chunker would ignore/split
   mid-thought? Recommend structure-aware chunking (by heading/section) if so.
2. Query-answer locality — for the sample queries, is the answer usually
   contained within one natural section, or does it require combining
   information across sections? This determines whether overlap or
   hierarchical retrieval is needed.
3. Special content — tables, code blocks, or lists that fixed-size
   chunking would corrupt (e.g. splitting a table mid-row) — recommend
   handling these as atomic units.
4. Metadata to attach per chunk — what metadata (source doc, section
   title, date) should travel with each chunk to support filtering or
   citation, given the query patterns.
5. Concrete recommendation: chunking method, target size, overlap
   percentage, and metadata schema — not just principles.
```

## Variants
- **Code documentation variant:** treat function/class definitions as atomic chunk boundaries regardless of size.
- **Long-document variant:** add hierarchical/parent-child chunking (small chunks for retrieval, larger parent context for generation).

## Related prompts
- [[rag-architecture-review]]
- [[vector-db-selection]]

## Tips
- Bring real sample documents, not a description of the format — structure-aware chunking depends on actually seeing headings/tables/code.
- Match chunk size to query-answer locality, not a generic "512 tokens" default.

## Common mistakes
- Using fixed-size chunking on structured documents (tables, code) and splitting semantic units mid-content.
- Omitting source/section metadata, making citation or filtered retrieval impossible later.

## Example
Input: technical documentation with clear H2 sections, queries usually answerable within one section.
Expected output: recommends section-based chunking (one chunk per H2, with heading path as metadata) over fixed 500-token chunks with overlap.

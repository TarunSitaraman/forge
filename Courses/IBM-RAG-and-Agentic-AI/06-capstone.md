# Capstone (Course 10)

*Once real implementation work starts, this becomes a live project.
Consider promoting it to `Projects/` (matching the `smartresq/` pattern)
the moment there's actual code — this file is planning-only until then.*

## Course 10: RAG and Agentic AI Capstone Project (14 hrs)

**Requirement (from IBM's program):** Architect an end-to-end Agentic
RAG system. Typically requires ingesting a corpus of unstructured,
domain-specific data (e.g., financial reports, medical documentation) and
combining multimodal data, vector databases, multi-agent systems, and
MCP configuration into one working system. Open-ended engineering
requirement, not a guided tutorial.

**Status:** Not started

## Planning

### Domain / Dataset Choice
*(What corpus will you use? Pick something you have genuine interest in
or existing domain knowledge of — makes evaluating "is this answer
actually good" much easier than a generic dataset.)*

- Candidate domains:
  - [ ] Option A:
  - [ ] Option B:
  - [ ] Option C:
- Decision:

### Architecture Plan

*(Sketch before building — which pieces from the curriculum actually
compose into this system?)*

```
[Corpus] → [Ingestion/Chunking] → [Vector DB] → ...
                                                  ↓
[Agent(s)] ← [MCP tools] ← [Multi-agent orchestration] ← ...
```

- Retrieval layer: (which vector DB — Chroma, FAISS — and why)
- Agent layer: (single agent with agentic RAG, or multi-agent via
  CrewAI/AutoGen/BeeAI — and why)
- Tool/MCP layer: (what tools does the agent need — search, calculator,
  domain-specific APIs?)
- Multimodal component (optional, if corpus includes non-text data):

### Milestones

- [ ] Corpus selected and ingested
- [ ] Chunking + embedding pipeline working, spot-checked for quality
- [ ] Baseline (non-agentic) RAG pipeline working end-to-end
- [ ] Agentic layer added (agent decides when/what to retrieve)
- [ ] MCP tool(s) wired in
- [ ] Multi-agent orchestration added (if architecture calls for it)
- [ ] Eval set built (labeled queries + expected answers/chunks)
- [ ] Retrieval recall/precision measured
- [ ] End-to-end demo working
- [ ] Write-up / submission prepared

### Success Criteria

*(What does "done and good" look like, concretely — not just "it runs"?
Tie back to `rag.md`'s guidance on building a real eval set rather than
eyeballing outputs.)*

## Promotion to Projects/

Once implementation begins in earnest:
1. Create `Projects/<capstone-name>/` following the `smartresq/`
   numbered-doc pattern (01-overview, 02-architecture, etc.) — scaled to
   this project's actual complexity, not forced to fill 10 docs.
2. Link back here from that project's `_index.md` under "Related
   Systems" the same way `smartresq/_index.md` links to `Systems/Docs/`.
3. Reduce this file to a pointer: "See `Projects/<name>/` for the live
   project — this section preserved for planning history only."

## Next

Once capstone is submitted → [Roadmap](./09-roadmap.md) for what comes
after the certificate.

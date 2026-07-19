# Curriculum Map

## Full Course Breakdown

| # | Course | Hours | Phase | Canonical doc(s) it feeds |
|---|---|---|---|---|
| 1 | Develop Generative AI Applications: Get Started | 10 | RAG Foundations | [`llms.md`](../../Technologies/Docs/llms.md), [`prompt-engineering.md`](../../Technologies/Docs/prompt-engineering.md), [`langchain.md`](../../Technologies/Docs/langchain.md) |
| 2 | Build RAG Applications: Get Started | 7 | RAG Foundations | [`rag.md`](../../Technologies/Docs/rag.md), [`langchain.md`](../../Technologies/Docs/langchain.md) |
| 3 | Vector Databases for RAG: An Introduction | 9 | RAG Foundations | [`vector-databases.md`](../../Technologies/Docs/vector-databases.md) |
| 4 | Advanced RAG with Vector Databases and Retrievers | 8 | RAG Foundations | [`vector-databases.md`](../../Technologies/Docs/vector-databases.md), [`rag.md`](../../Technologies/Docs/rag.md) |
| 5 | Build Multimodal Generative AI Applications | 8 | Multimodal | *(none yet — candidate for new `multimodal-ai.md`)* |
| 6 | Fundamentals of Building AI Agents | 11 | Agentic AI | [`ai-agents.md`](../../Technologies/Docs/ai-agents.md) |
| 7 | Agentic AI with LangChain and LangGraph | 11 | Agentic AI | [`ai-agents.md`](../../Technologies/Docs/ai-agents.md) |
| 8 | Agentic AI with LangGraph, CrewAI, AutoGen and BeeAI | 13 | Agentic AI | [`ai-agents.md`](../../Technologies/Docs/ai-agents.md) |
| 9 | Build AI Agents using MCP | 10 | Agentic AI | [`ai-agents.md`](../../Technologies/Docs/ai-agents.md) |
| 10 | RAG and Agentic AI Capstone Project | 14 | Capstone | Project — see [Capstone](./06-capstone.md), promotes to `Projects/` |

**Total:** ~109 hours.

## Phase Grouping & Why the Sequencing Makes Sense

```
Phase 1: RAG Foundations (Courses 1-4, 34 hrs)
  Course 1: GenAI basics + LangChain fundamentals + prompt engineering
      ↓ (need basic LangChain fluency before building RAG)
  Course 2: RAG introduced — LangChain + LlamaIndex RAG chains
      ↓ (need to understand *why* retrieval quality matters)
  Course 3: Vector DB internals — ChromaDB, similarity search
      ↓ (foundations solid, now go deeper on retrieval quality)
  Course 4: Advanced retrievers, FAISS, HNSW indexing

Phase 2: Multimodal (Course 5, 8 hrs)
  Independent branch — extends the GenAI app layer to non-text modalities
  (Whisper, DALL·E, Sora). Doesn't block or get blocked by agentic track.

Phase 3: Agentic AI (Courses 6-9, 45 hrs)
  Course 6: Agent fundamentals — tool calling, the basic reason/act loop
      ↓ (need the loop before you need to make it branch/loop robustly)
  Course 7: LangGraph — state machines, ReAct/Reflection/Reflexion
      ↓ (single-agent patterns solid, now split work across agents)
  Course 8: Multi-agent frameworks — CrewAI, AutoGen, BeeAI
      ↓ (agents now need a standard way to reach external tools/data)
  Course 9: MCP — standardized tool/context protocol across agents

Phase 4: Capstone (Course 10, 14 hrs)
  Requires everything above: RAG pipeline + vector DB + (optionally)
  multimodal ingestion + multi-agent orchestration + MCP tool wiring.
```

**Key dependency to respect:** don't skip ahead to Course 6+ before
Courses 1–4 are solid. Agentic RAG (an agent deciding when/what to
retrieve) only makes sense once you understand what a *non*-agentic,
fixed RAG pipeline looks like and where it breaks down.

## Where This Diverges From a Generic RAG/Agent Course

IBM-specific angle to watch for and capture in the track docs (not
generic RAG/agent theory, which lives in the canonical docs):
- **watsonx.ai** as the model/tooling platform — how IBM's stack differs
  from a raw OpenAI/Anthropic API integration.
- **Granite models** — IBM's own model family, used across labs; note
  where behavior/capabilities differ from Llama or GPT-family models used
  elsewhere in the curriculum.
- Labs are notebook-based (Jupyter) — implementation details (specific
  library versions, watsonx auth/setup quirks) belong in the track docs,
  not the canonical references (which stay framework-agnostic where
  possible).

## Next

- Starting Phase 1 → [RAG & Vector DB Track](./03-rag-and-vector-db-track.md)
- Starting Phase 2 → [Multimodal Track](./04-multimodal-track.md)
- Starting Phase 3 → [Agentic AI Track](./05-agentic-ai-track.md)
- Starting Phase 4 → [Capstone](./06-capstone.md)

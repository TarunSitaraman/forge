# Glossary

*Quick lookup for terms and acronyms encountered across the certificate.
For full conceptual depth, follow the link to the canonical doc — this
is a fast-reference index, not a replacement for those docs.*

## RAG & Vector DB Terms

| Term | Quick definition | Full reference |
|---|---|---|
| RAG | Retrieval-Augmented Generation — ground LLM output in retrieved external content | [`rag.md`](../../Technologies/Docs/rag.md) |
| Chunking | Splitting source documents into retrievable units | [`rag.md`](../../Technologies/Docs/rag.md) |
| top-k | Number of chunks retrieved per query | [`rag.md`](../../Technologies/Docs/rag.md) |
| Reranker | Second-pass model that reorders retrieved candidates by relevance | [`rag.md`](../../Technologies/Docs/rag.md) |
| HNSW | Hierarchical Navigable Small World — approximate nearest-neighbor index algorithm | [`vector-databases.md`](../../Technologies/Docs/vector-databases.md) |
| FAISS | Facebook AI Similarity Search — vector similarity search library | [`vector-databases.md`](../../Technologies/Docs/vector-databases.md) |
| ChromaDB | Open-source vector database used across the RAG course labs | [`vector-databases.md`](../../Technologies/Docs/vector-databases.md) |
| Hybrid search | Combining keyword (BM25) and vector similarity search | [`rag.md`](../../Technologies/Docs/rag.md) |

## Agentic AI Terms

| Term | Quick definition | Full reference |
|---|---|---|
| ReAct | Reason + Act loop — agent reasons about next step, acts, observes, repeats | [`ai-agents.md`](../../Technologies/Docs/ai-agents.md) |
| Reflection | Agent self-critiques its own output within one generation episode | [`ai-agents.md`](../../Technologies/Docs/ai-agents.md) |
| Reflexion | Reflection + persisted feedback across multiple attempts/episodes | [`ai-agents.md`](../../Technologies/Docs/ai-agents.md) |
| LangGraph | State-machine framework for agents that need branching/looping | [`ai-agents.md`](../../Technologies/Docs/ai-agents.md) |
| CrewAI | Role-based multi-agent framework (sequential/hierarchical process) | [`ai-agents.md`](../../Technologies/Docs/ai-agents.md) |
| AutoGen / AG2 | Conversational multi-agent framework | [`ai-agents.md`](../../Technologies/Docs/ai-agents.md) |
| BeeAI | Standardized, framework-agnostic agent definition format | [`ai-agents.md`](../../Technologies/Docs/ai-agents.md) |
| MCP | Model Context Protocol — standard protocol for agent-to-tool/resource communication | [`ai-agents.md`](../../Technologies/Docs/ai-agents.md) |
| FastMCP | Python framework for building MCP servers quickly | [`ai-agents.md`](../../Technologies/Docs/ai-agents.md) |
| Agentic RAG | RAG where an agent decides when/what to retrieve, vs. a fixed pipeline | [`rag.md`](../../Technologies/Docs/rag.md), [`ai-agents.md`](../../Technologies/Docs/ai-agents.md) |

## IBM / watsonx-Specific Terms

*(Fill in as encountered — these are platform-specific and won't have a
canonical Forge doc; this glossary is their primary home.)*

| Term | Quick definition |
|---|---|
| watsonx.ai | |
| Granite | |
| | |

## Multimodal Terms

| Term | Quick definition | Full reference |
|---|---|---|
| Whisper | Speech-to-text model | [Multimodal Track](./04-multimodal-track.md) *(no canonical doc yet)* |
| DALL·E | Text-to-image generation model | [Multimodal Track](./04-multimodal-track.md) |
| Sora | Text-to-video generation model | [Multimodal Track](./04-multimodal-track.md) |

## Maintenance

Add a term the moment you hit an unfamiliar acronym in a lab or video —
don't wait until end of course. Move a term's "Full reference" column to
point at a canonical doc the moment that doc actually covers it (many of
these will move from "Multimodal Track" to a future `multimodal-ai.md`,
for instance).

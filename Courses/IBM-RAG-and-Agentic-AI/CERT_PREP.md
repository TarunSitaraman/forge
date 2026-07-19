# Certification Prep — Interview Readiness

**Use this to check whether the certificate actually made you interview-ready
on RAG and agentic AI topics, not just lab-completion-ready.**

*Ties into [`Career/technical-interview-prep-checklist.md`](../../Career/technical-interview-prep-checklist.md)
for general technical interview process; this file is scoped to the
specific technical content this certificate covers.*

---

## Elevator Pitch (what you should be able to say, unprompted)

*(Fill in once you're through Phase 1 and Phase 3 — write this in your
own words, don't just paraphrase the course description.)*

"I completed IBM's RAG and Agentic AI certificate, which covered
end-to-end RAG pipeline design (chunking, embeddings, vector databases,
retrieval, reranking), and agentic AI systems — single-agent patterns
(ReAct, Reflection, Reflexion), multi-agent orchestration (CrewAI,
AutoGen, BeeAI), and the Model Context Protocol for standardized
tool/resource access. The capstone required [fill in what you actually
built]."

---

## RAG Questions You Should Be Able to Answer Cold

*(These mirror the Interview Questions section already in `rag.md` —
verify you can answer them after the course, don't just read them.)*

1. A RAG system gives a wrong answer — how do you determine if it's a
   retrieval or generation problem?
2. Why might increasing top-k make answers worse, not better?
3. When would you use hybrid (keyword + vector) search over pure vector
   search?
4. How do you evaluate a RAG system beyond "the answers look good to me"?
5. How do you reduce hallucination in a RAG system when the corpus
   genuinely doesn't contain the answer?

**Your answers (write these out, don't just check the box):**

---

## Agentic AI Questions You Should Be Able to Answer Cold

*(Mirrors the Interview Questions section in `ai-agents.md`.)*

1. What's the difference between a LangChain chain and a LangGraph graph,
   and when do you need the latter?
2. Explain the difference between Reflection and Reflexion.
3. When would you choose a multi-agent system over a single agent with
   more tools?
4. What problem does MCP solve that framework-specific tool calling
   doesn't?
5. An agent keeps failing a task — how do you determine if it's a
   reasoning failure or a tooling failure?
6. Why cap the number of agent iterations?

**Your answers:**

---

## Vector Database Questions

1. When would you choose FAISS over ChromaDB, or vice versa?
2. What does HNSW indexing trade off against exact nearest-neighbor
   search?
3. How does embedding model choice affect retrieval quality for a
   specific domain's vocabulary?

**Your answers:**

---

## System Design Style Questions (synthesize across the whole cert)

1. **Design an agentic RAG system for [a domain from your capstone].**
   Walk through: ingestion, chunking strategy, vector DB choice,
   retrieval approach, when the agent decides to retrieve vs. answer
   directly, what tools it has access to, and how you'd evaluate the
   whole system.
2. **Your agentic RAG system is giving inconsistent answers to the same
   question asked twice. Debug this out loud.** *(Expected reasoning
   path: check retrieval determinism → check if agent's tool-use path
   varies → check temperature/sampling settings → check if context
   window is truncating differently between runs.)*
3. **A stakeholder asks why you didn't just use a single large-context
   LLM instead of RAG.** *(Expected: cost of reprocessing full corpus per
   query, staleness — corpus updates need reprocessing either way,
   citation/traceability requirements, and context window limits for
   genuinely large corpora.)*
4. **A stakeholder asks why you used multiple agents instead of one
   agent with more tools.** *(Expected: articulate the actual
   coordination overhead vs. benefit tradeoff from
   `ai-agents.md`'s Best Practices — don't just say "it's more
   scalable" without justifying against the specific task.)*

**Your answers:**

---

## Capstone Talking Points

*(Fill in after completing the capstone — this becomes your primary
concrete example in interviews, more valuable than any lab exercise.)*

- **What you built:**
- **Why this architecture (not an alternative):**
- **What broke first, and how you found it:**
- **What you'd do differently with more time:**
- **Metrics/eval results (recall@k, answer quality, latency):**

---

## Self-Check: Readiness Gate

Before claiming this certificate in an interview context, confirm:

- [ ] Can explain retrieval vs. generation failure diagnosis without
      looking anything up
- [ ] Can explain ReAct/Reflection/Reflexion differences without
      looking anything up
- [ ] Can justify a specific vector DB choice for a hypothetical use case
- [ ] Can justify single-agent vs. multi-agent for a hypothetical use
      case
- [ ] Can explain MCP's value proposition in one sentence
- [ ] Has a concrete capstone story with real metrics, not just "it
      worked"

If any box is unchecked, that's a specific gap to close before this
certificate is interview-ready — not just Coursera-complete.

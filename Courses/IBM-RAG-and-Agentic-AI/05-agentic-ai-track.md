# Agentic AI Track (Courses 6–9)

*This doc tracks course-specific implementation notes and lab progress
only. Agent architecture theory (ReAct, Reflection, Reflexion,
LangGraph, multi-agent frameworks, MCP) lives in
[`Technologies/Docs/ai-agents.md`](../../Technologies/Docs/ai-agents.md)
— merge anything genuinely new and reusable there, not here.*

## Course 6: Fundamentals of Building AI Agents (11 hrs)

**Covers:** Autonomous AI agents, tool calling, chaining, LangChain
agents for data analysis, visualization, and database query execution.

**Status:** Not started

**Labs/Notebooks:**
- [ ] Lab 1:
- [ ] Lab 2:
- [ ] Lab 3:

**IBM/watsonx-specific notes:**

**Key takeaways:**

---

## Course 7: Agentic AI with LangChain and LangGraph (11 hrs)

**Covers:** Agentic systems using LangChain and LangGraph, including
Reflection, Reflexion, and ReAct architectures; building self-improving
agents; conditional logic and looping in LangGraph state machines;
wiring up agentic RAG systems.

**Status:** Not started

**Labs/Notebooks:**
- [ ] Lab 1:
- [ ] Lab 2:
- [ ] Lab 3:

**IBM/watsonx-specific notes:**

**Agentic RAG notes:** *(this course explicitly connects back to the RAG
track — capture how an agent decides when/what to retrieve vs. the fixed
pipeline from Course 2/4; this is the intersection point noted in both
`rag.md` and `ai-agents.md`'s Further Reading sections)*

**Key takeaways:**

---

## Course 8: Agentic AI with LangGraph, CrewAI, AutoGen and BeeAI (13 hrs)

**Covers:** Multi-agent workflows using CrewAI, BeeAI, and AG2 (AutoGen)
frameworks, custom tools, conversation-driven interactions.

**Status:** Not started

**Labs/Notebooks:**
- [ ] Lab 1:
- [ ] Lab 2:
- [ ] Lab 3:

**IBM/watsonx-specific notes:**

**Framework comparison notes (CrewAI vs AutoGen vs BeeAI):**

| Framework | Coordination pattern | When it felt like the right fit |
|---|---|---|
| CrewAI | Role-based crew, sequential/hierarchical | |
| AutoGen (AG2) | Conversational, agents message each other | |
| BeeAI | Standardized, framework-agnostic agent defs | |

*(Candidate addition to `ai-agents.md`'s cheatsheet if this comparison
sharpens beyond what's already there)*

**Key takeaways:**

---

## Course 9: Build AI Agents using MCP (10 hrs)

**Covers:** Model Context Protocol architecture, FastMCP servers,
configuring tools and resources, STDIO and Streamable HTTP clients,
multi-server orchestration with permission-gating, AI security
(authorization, permission-based approval flows).

**Status:** Not started

**Labs/Notebooks:**
- [ ] Lab 1:
- [ ] Lab 2:
- [ ] Lab 3:

**IBM/watsonx-specific notes:**

**MCP security notes:** *(this course goes deeper on permission-gating
and authorization than the current `ai-agents.md` MCP section — likely
candidate for a real expansion there, not just a course note)*

**Key takeaways:**

---

## Phase Summary (fill in after completing all 4 courses)

**What this phase actually taught beyond what was already in
`ai-agents.md`:**

**What merged into `ai-agents.md` (with links to the specific section):**

**What stayed here as IBM/watsonx-specific implementation detail:**

## Next

Once this phase is done → [Capstone](./06-capstone.md).

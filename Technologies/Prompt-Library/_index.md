# Prompt Library

*Operating procedures, not generic prompts. Every entry here is written the
way a senior engineer or consultant would actually approach the task —
with judgment calls, failure modes, and the specific evidence to demand
before acting.*

A prompt earns a place here only if it encodes real expertise: a specific
process, the mistakes that commonly happen, and how to tell good output
from bad. "Write me a README" is not a library entry. "Turn a working
diff into a senior-engineer-grade PR review with an explicit blocking/
non-blocking verdict" is.

## Categories

| Category | Focus |
|---|---|
| [`Software-Engineering/`](Software-Engineering) | Code review, simplification, onboarding to unfamiliar code |
| [`Debugging/`](Debugging) | Root-causing, incident triage, flaky test diagnosis |
| [`Architecture/`](Architecture) | ADRs, service boundaries, scalability stress-testing |
| [`Cloud/`](Cloud) | Cost audits, migration planning |
| [`Azure/`](Azure) | Resource config review, pipeline hardening |
| [`Databricks/`](Databricks) | Job cost/performance, Delta Lake table design |
| [`AI/`](AI) | LLM feature feasibility, eval design, hallucination mitigation |
| [`RAG/`](RAG) | Retrieval architecture diagnosis, chunking strategy |
| [`Vector-Databases/`](Vector-Databases) | Vector DB selection, embedding model evaluation |
| [`Research/`](Research) | Literature synthesis, question refinement, critical reading |
| [`Writing/`](Writing) | Prose tightening, technical explanation for non-experts |
| [`Email/`](Email) | Actionable emails, difficult conversations |
| [`Presentations/`](Presentations) | Narrative arc, demo scripting |
| [`System-Design/`](System-Design) | Design walkthroughs, API contract review, data modeling |
| [`API-Design/`](API-Design) | Designing a new API from scratch, versioning strategy |
| [`Documentation/`](Documentation) | Runbooks, doc outlines from real codebases |
| [`Career/`](Career) | Resume, LinkedIn, portfolio, networking, cold emails, salary research, offer comparison, career planning — see also `Career/` for the tracker module these support |
| [`Interview/`](Interview) | Behavioral stories, mock technical interviews, interviewer question banks |
| [`Hackathons/`](Hackathons) | Idea triage, execution timelines |
| [`Competitive-Programming/`](Competitive-Programming) | Hint-only coaching, post-solution analysis, complexity/code review, weakness-finding, interview variants — see also `Courses/Competitive-Programming/` for the tracker module these support |
| [`Project-Planning/`](Project-Planning) | Scope cuts, dependency/risk mapping |
| [`Decision-Making/`](Decision-Making) | Tradeoff matrices, pre-mortems |
| [`Prompt-Engineering/`](Prompt-Engineering) | Prompt failure diagnosis, system prompt review, injection hardening |
| [`Refactoring/`](Refactoring) | Safe legacy-code refactors, reusable abstraction extraction |
| [`Performance-Optimization/`](Performance-Optimization) | Bottleneck diagnosis, optimization tradeoff review |
| [`Docker/`](Docker) | Dockerfile review, container debugging — see also `../Docs/docker.md` |
| [`Git/`](Git) | Commit history cleanup, merge conflict resolution — see also `../Docs/git.md` |
| [`Learning/`](Learning) | Mental model building, learning plan generation |

## Every entry follows the same shape

Title, Purpose, When to use, Expected input, Expected output, the
complete prompt, Variants, Related prompts, Tips, Common mistakes, and a
worked Example. Skim any file top to bottom and you can use it in under a
minute — that's the bar.

## Using a prompt

1. Find the category, find the entry closest to your situation.
2. Fill in the placeholders in "The complete prompt" — don't skip the
   context fields; they're what make the output specific instead of
   generic.
3. Check "Common mistakes" before you run it — most of them are about
   under-specifying context, not the prompt itself.

## Adding a new entry

Only add a prompt here once you've used it successfully more than once —
a one-off prompt belongs in the project it was written for, not here.
Follow the exact section structure of an existing file in the same
category so the library stays scannable.

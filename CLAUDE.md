# Forge — Instructions for Claude

*Project-level context for Claude Code sessions in this repository.
Read this before making changes — it captures conventions and repo
state that aren't obvious from a fresh clone, and mistakes already
learned so they aren't repeated.*

## What Forge Is

A canonical, production-grade personal engineering knowledge base.
Git-first, Obsidian-compatible, Markdown-only. Three governing
principles (see `README.md` for full framing):

1. **Canonical** — one authoritative home per concept, no duplicates.
2. **Production-grade** — every doc meets real-world reference quality.
3. **Instantly retrievable** — organized for finding things fast, not
   for chronicling thought.

Read `START_HERE.md`, `CONVENTIONS.md`, and `WORKFLOW.md` before adding
content if this is your first time in this repo — they're short and
this file assumes you've read them.

## The No-Duplication Rule (most important, most often violated)

**One concept → one canonical file.** Before writing a technical
explanation anywhere, check `Technologies/Docs/_index.md` first — if
the technology/concept already has a doc there, extend that file
instead of writing a new one, and link to it rather than re-explaining
it. This applies even under `Projects/` and `Courses/`: those folders
hold *implementation notes and progress*, not conceptual explanations.

**Concrete split that trips people up:**
- `Technologies/Docs/` — durable, technology-scoped reference (RAG,
  vector DBs, LangChain, AI agents, LLMs, prompt engineering, etc.).
  One file per technology, structured Overview → Mental Model →
  Architecture → Common Workflows → Common Mistakes → Best Practices
  → Cheatsheet → Interview Questions → Further Reading.
- `Courses/` — progress trackers and course-specific implementation
  notes for structured learning (e.g. a Coursera cert). Explicitly NOT
  where technical explanations belong — those get merged into
  `Technologies/Docs/` as they're learned.
- `Resources/courses.md` — a curated *pointer* to an external course,
  added only *after* completing it, one row, stating specifically why
  it earned its place. Not a notes repository.
- `Projects/` — active build work. Each project gets its own
  numbered-doc knowledge pack if it's substantial (see pattern below).

## Known Stale/Legacy Item

`Systems/` at repo root is a **leftover from a prior reorganization** —
its `_index.md` describes subfolders (`Docs/`, `Playbooks/`,
`Prompt-Library/`, etc.) that no longer exist under it; that content
now lives under top-level `Technologies/`. `Projects/smartresq/`'s
`_index.md` and `INTERVIEW_GUIDE.md` still contain broken
`../../Systems/Docs/...` links from before the move — they should
point to `../../Technologies/Docs/...` instead. Not yet fixed; fix
opportunistically if you're already editing those files, or on request.

`README.md`'s DSA folder numbering (`05_Mistakes`, `06_Cheat_Sheets`)
is also stale — actual folders are `08_Mistakes` and `09_CheatSheets`.
Its Courses section only lists Competitive-Programming, missing
IBM-RAG-and-Agentic-AI. Don't trust README.md's specific counts; verify
against the filesystem (see current stats below) or update it if you
have a reason to be in there anyway.

## The "Knowledge Pack" Pattern

`Projects/smartresq/` is the reference implementation of Forge's
deepest content pattern: numbered docs (`01-overview.md` →
`10-roadmap.md`) plus an `_index.md` hub (status, tags, key metrics
table, at-a-glance diagram, quick nav) and a bonus guide file
(`INTERVIEW_GUIDE.md`). ~150–300 lines per doc.

This pattern has been adapted twice more:
- **DSA/** — pattern-level knowledge packs (pattern page + representative
  problems index + detailed problems + interview guide + cheat sheet),
  applied uniformly across all 32 patterns.
- **`Courses/IBM-RAG-and-Agentic-AI/`** — curriculum-scoped adaptation
  (overview, curriculum map, per-phase track docs, capstone planning,
  glossary, progress tracker, roadmap, cert-prep guide). Scaled down
  from SmartResQ's ~2500 lines to ~930, since most technical depth
  properly routes out to `Technologies/Docs/` rather than living in the
  course folder itself.

**When asked for this pattern elsewhere:** scale the doc count and
depth to the subject's actual complexity — don't force exactly 10 docs
padded with filler. Always include the `_index.md` hub with a metrics
table and quick nav; that's the load-bearing piece for fast retrieval.

## Current Repository State (verify before trusting exact numbers — this section will drift)

**DSA/** (flagship section, all 32 patterns at full baseline coverage):
- 32 patterns, 32 representative-problem indices, 32 interview guides
- 70 detailed problem pages (DFS and BFS have 5 each — deepest
  coverage; most other patterns have 1–3, expansion is ongoing and
  welcome — prioritize Hashing, Heap, Matrix Traversal, Trie next if
  picking up this work)
- 39 cheat sheets (32 pattern-specific + ~7 auxiliary reference sheets
  like Bisect, Complexities, Python Collections)
- Status docs with full session history:
  `DSA_IMPLEMENTATION_PLAN.md`, `FORGE_COMPLETION_STATUS.md`,
  `FORGE_SESSION_2_SUMMARY.md`, `FORGE_SESSION_3_FINAL_SUMMARY.md`

**Technologies/Docs/** — 11 canonical references: Azure, Databricks,
Docker, Git, LangChain, RAG, **AI Agents** (added this session — covers
ReAct/Reflection/Reflexion, LangGraph, CrewAI/AutoGen/BeeAI, MCP),
Vector Databases, LLMs, Prompt Engineering, Markdown.

**Courses/** — Competitive-Programming (skill tracker) and
IBM-RAG-and-Agentic-AI (11-file knowledge pack, 0/10 courses complete
as of creation — check `Courses/IBM-RAG-and-Agentic-AI/08-progress-tracker.md`
for current status before assuming it's still at zero).

## Git Workflow Notes

- This repo has an active GitHub remote (`origin/main`); commits made
  during sessions have generally been pushed immediately after each
  logical batch, not held until end-of-session.
- Commit messages in this repo's history follow: one-line conventional
  summary, blank line, bullet list of what changed and why, ending with
  `Co-Authored-By: Claude <model> <noreply@anthropic.com>`. Match this
  style.
- Watch for **unrelated untracked/modified files** appearing in
  `git status` that you didn't create (this has happened — e.g.
  `GITHUB_PROFILE.md`, a modified `README.md` — from some other process
  or session touching the repo). Don't sweep them into your commit;
  stage and commit only what you actually created/changed for the
  current task.
- LF/CRLF warnings on `git add` are expected on this Windows checkout
  and harmless — not a sign of a real problem.

## Content Quality Bar (applies everywhere, especially DSA and Docs)

- WHY before HOW — explain why a technique/pattern applies before
  showing code.
- Every technical doc needs: definition/overview, a genuine mental
  model (not just a restatement of the overview), common mistakes with
  concrete wrong/right contrast, and cross-links to related canonical
  docs.
- No filler, no TODOs left in "finished" content — either fill it in or
  leave an explicit `*(fill in — reason)*` placeholder that's honest
  about being incomplete (this is the convention used throughout the
  Courses knowledge pack for content that only exists after you
  actually do the course).

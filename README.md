# Forge

**A canonical, production-grade engineering knowledge base. Git-first, Obsidian-compatible, no fluff.**

Forge is your searchable engineering brain built on three principles:

1. **Canonical** — One authoritative home per concept. No duplicates, no scattered notes.
2. **Production-grade** — Every explanation, solution, and interview guide meets real-world standards.
3. **Instantly retrievable** — Built for execution: find what you need *right now*, or learn what you'll execute faster *next time*.

Forge is *not* a notes vault, journal, or thought dump. Every file here either helps you execute immediately or captures something reusable for the next time. If a file does neither, it doesn't belong here.

---

## What's Inside: DSA Knowledge Base

The flagship section is a **comprehensive Data Structures and Algorithms knowledge base** built for interview preparation and competitive programming.

### DSA Coverage

| Component | Count |
|-----------|-------|
| **Patterns** | 32 (Two Pointers, Sliding Window, Binary Search, DFS, BFS, Dynamic Programming, Graph Traversal, Topological Sort, Union Find, Greedy, Backtracking, Memoization, and more) |
| **Detailed Problems** | 85 (with solutions, complexity analysis, edge cases, and interview walkthroughs) |
| **Pattern-Specific Interview Guides** | 32 (recognition criteria, communication templates, common mistakes) |
| **Complexity Cheat Sheets** | 39 (32 pattern-specific + 7 auxiliary quick-reference sheets) |
| **Python Templates** | 30 (production-quality implementations) |
| **Algorithms** | 30 (named, reusable techniques) |
| **Data Structures** | 18 (operation contracts, invariants, tradeoffs) |
| **Mistake Encyclopedia** | 12 common pitfalls with concrete examples |

Every problem page includes: complete problem statement with constraints, why the pattern applies (theory), a commented Python solution, complexity analysis with justification, edge cases, common mistakes, and an interview walkthrough dialogue.

### Quick Start

- **New to DSA?** Start at [`DSA/00_Index/DSA Home`](DSA/00_Index/DSA%20Home.md)
- **Interview prep?** Use the pattern-specific interview guides and representative problems
- **Looking for a solution template?** Pattern pages link to well-commented Python code
- **Stuck on a problem?** Check the representative problems section for your pattern

---

## Design Principles

- **Git-first.** The repository is the source of truth. History, branches, and diffs track change — not backlinks, plugin metadata, or a database.
- **Obsidian-compatible.** Open as an Obsidian vault for graph view and backlinks, but every file works as plain Markdown in any editor, on GitHub, or via `grep`. Obsidian is optional tooling, not a dependency.
- **Markdown only.** No databases, proprietary formats, or plugin lock-in. Content survives any tool change.
- **Minimal plugins.** See [`.obsidian-config/`](.obsidian-config) for the short, deliberate list. Every plugin must earn its place.
- **Fast navigation.** Simple folder structure, consistent naming, no deep nesting. Finding what you need should be instant.
- **No bloat.** One canonical home per concept. No duplicates, no orphaned files, no thought dumps. Quality over volume.

## What Forge is not

- Not a daily journal or thought dump.
- Not a personal wiki of every idea you've ever had.
- Not a task manager or calendar.
- Not a database of half-formed notes waiting to "compost."

Forge is where *finished thinking* becomes reusable engineering material. If you want to write freely, write elsewhere.

---

## Repository Structure

Organization is retrieval-first: folders represent where you *find* things based on your current context.

```
forge/
├── README.md               You are here.
├── CLAUDE.md                Project context for Claude Code sessions.
├── START_HERE.md           Onboarding + daily entry point.
├── CONVENTIONS.md          Naming, Markdown, and tagging rules.
├── WORKFLOW.md             Git workflow for using Forge day to day.
├── ROADMAP.md              Where Forge is headed.
├── .obsidian-config/       Minimal, version-controlled Obsidian setup.
│
├── DSA/                    Data Structures & Algorithms (flagship section)
│   ├── 00_Index/           DSA Home, Learning Paths, Quick Reference
│   ├── 01_Patterns/        32 core patterns (Two Pointers, DFS, DP, etc.)
│   ├── 02_Algorithms/      30 algorithm implementations
│   ├── 03_DataStructures/  18 data structure implementations
│   ├── 04_Problems/        85 detailed problems + 32 representative-problem indices
│   ├── 05_Templates/       Python implementation templates
│   ├── 06_Complexity/      Time/space complexity reference
│   ├── 07_Interview/       Pattern-specific interview guides
│   ├── 08_Mistakes/        Common pitfalls and how to avoid them
│   └── 09_CheatSheets/     Quick reference guides per pattern
│
├── Technologies/           Reusable technical systems
│   ├── Prompt-Library/     AI prompts organized by domain
│   ├── Playbooks/          Execution workflows with diagrams
│   ├── Templates/          Reusable Markdown document templates
│   ├── Docs/               Authoritative reference manuals (one file per technology)
│   └── Project-System/     Engineering notebook template
│
├── Projects/               Active build work. One folder per project.
├── Courses/                Structured learning: progress trackers, not content.
├── Career/                 Resume, LinkedIn, portfolio, interview, salary tools.
├── Resources/              Curated external resources across 11 categories.
├── Reference/              Durable technical facts: API notes, configs, gotchas.
├── Inbox/                  Unsorted capture. Must be emptied on a cadence.
└── Archive/                Completed or dead projects.
```

**Each folder has an `_index.md`** explaining its purpose, scope, and what doesn't belong. Read before adding files.

## Technologies Module

Reusable engineering systems for building and shipping.

| Module | Description |
|--------|-------------|
| **Prompt-Library/** | AI prompts across many categories (operating-procedure grade) |
| **Playbooks/** | Execution workflows for recurring tasks, each with a Mermaid diagram |
| **Templates/** | Production-quality Markdown document templates |
| **Docs/** | Authoritative reference manuals: Azure, Databricks, Docker, Git, LangChain, RAG, AI Agents, Vector Databases, LLMs, Prompt Engineering, Markdown |
| **Project-System/** | The engineering-notebook template every project scaffolds from |

## Courses Module

Structured learning for deliberate skill development — progress trackers and course-specific notes, not technical reference material (that lives in `Technologies/Docs/`).

| Module | What it is |
|---|---|
| [`Competitive-Programming/`](Courses/Competitive-Programming) | Problem-solving skill trackers and coaching prompts |
| [`IBM-RAG-and-Agentic-AI/`](Courses/IBM-RAG-and-Agentic-AI) | Knowledge-pack-style tracker for the IBM RAG and Agentic AI Professional Certificate (10 courses) |

## Career Module

Actionable career development tools: resume, LinkedIn, portfolio, interviews, salary negotiation, and planning.
See [`Career/_index.md`](Career/_index.md) for details.

## Resources Module

Curated external resources across 11 categories (docs, repos, courses, blogs, papers, books).
See [`Resources/_index.md`](Resources/_index.md) for the full index.

---

## Getting Started

### If you're here for DSA prep:
1. Start at [`DSA/00_Index/DSA Home`](DSA/00_Index/DSA%20Home.md)
2. Pick a pattern that matches your current problem
3. Read the pattern page, study the representative problems, and use the interview guide

### If you're new to Forge:
1. Read [`START_HERE.md`](START_HERE.md) — your entry point
2. Read [`CONVENTIONS.md`](CONVENTIONS.md) — naming, structure, and tagging rules
3. Read [`WORKFLOW.md`](WORKFLOW.md) — how to commit and use Forge day to day

### If you're adding content:
1. Read [`CONVENTIONS.md`](CONVENTIONS.md) first — consistency is enforced by discipline, not tooling
2. Find the appropriate folder and read its `_index.md`
3. Follow the structure and naming conventions for your folder
4. Commit using the patterns in [`WORKFLOW.md`](WORKFLOW.md)

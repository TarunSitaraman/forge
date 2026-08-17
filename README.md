# Forge

**A local-first knowledge OS that maintains understanding, not just files.**

Forge ingests sources, extracts claims with page-level provenance, links them
into a knowledge graph — and when new evidence contradicts something it already
believed, it *tells you* instead of silently overwriting it.

It is two things in one repository, and the relationship between them is the
point:

| | What it is |
|---|---|
| **The engine** (`engine/`) | A Python knowledge OS. ~18,000 lines, 763 offline tests. Reads the vault, derives a provenance-aware model from it, and never writes back without explicit approval. |
| **The vault** (everything else) | A hand-authored engineering knowledge base — 646 Markdown files, ~57,600 lines. The engine's first ingestion source, its primary evaluation set, and the reason it exists. |

The engine was not built against a toy corpus. It was built against a real one
that had already drifted, and its correctness target is the set of rules that
corpus was supposed to follow.

---

## Why this exists

Forge began as a disciplined Markdown vault: one canonical file per concept,
enforced by hand. Then a [full audit](docs/architecture/forge-current-state.md)
measured what a year of that discipline actually produced:

> 145 broken wikilinks · 42% of files with no machine-readable metadata ·
> 283 malformed relationship fields · stale counts in three separate files

The rules in [`CONVENTIONS.md`](CONVENTIONS.md) were never wrong. They were
**unenforceable by hand at that scale**. The engine's first job is to enforce
mechanically what this repository already specifies — and its later jobs follow
from the same premise: a knowledge base that cannot check itself will drift, and
one that silently accepts every new source will rot faster than one that
maintains nothing.

## The rules the engine is built on

These are enforced in code and asserted in tests, not stated as aspirations:

- **The vault is read-only to the engine.** Tests byte-compare every Markdown
  file before and after every operation. Derived state lives in `.forge/` and is
  rebuildable — delete it and nothing of value is lost.
- **Provenance floor.** A derived object can never claim stronger provenance
  than its weakest input. Enforced in a pydantic validator, so a violating
  object *cannot be constructed*.
- **A model may never assert `SOURCE_FACT` or `USER_ASSERTION`.** Those tiers
  belong to sources and humans.
- **Nothing is stored without evidence.** A claim whose quote cannot be found in
  its source is dropped, and the drop is reported.
- **Model reasoning never mutates knowledge directly.** It produces a proposal;
  a human approves; activation applies it.
- **Deterministic work stays deterministic.** Parsing, hashing, chunking,
  matching, graph traversal, and impact classification make **zero** LLM calls —
  and the tests assert the call count, so a future refactor cannot quietly
  introduce one.
- **No measurement claim without a measurement.** Where something could not be
  measured, [`docs/research/`](docs/research/) records it as unmeasured rather
  than estimating it.

## What it does

```bash
pip install -e ".[dev]"          # Python 3.10+

forge index                     # deterministic index; reports "LLM calls: 0"
forge diagnostics               # every frontmatter and link defect
forge corpus-stats              # counts from the filesystem, never from a doc
forge ingest paper.pdf          # spans carrying page + section provenance
forge search "chunking"         # evidence with citations, not generated prose
forge proposals list            # what Forge would change, awaiting your call
forge activate                  # approved proposals -> canonical knowledge
forge concept "RAG"             # what Forge knows, and which page proved it
forge graph path A B            # how two concepts connect
forge retrieval-eval            # measured retrieval quality, not a claim
forge evolve paper-b.pdf        # how does this paper affect what I know?
forge workflow inspect <id>     # why did Forge propose that?
```

**Conflict handling is the load-bearing feature.** When a new paper disagrees
with something Forge already believes, it does not overwrite it and does not
quietly accept it. It reports a *potential conflict*, shows you the page that
prompted it, and waits. Approve, and the original claim is marked disputed with
the new evidence attached — never rewritten, never retracted. The whole run
replays afterwards with `forge workflow inspect`.

Ambiguous concepts are never merged silently either. This vault has four real
collisions; Forge documents each one and waits for a decision, which is then
persisted in [`config/concept-identity.yaml`](config/) and versioned alongside
the vault.

### Measured, including the negative results

Retrieval quality is evaluated against a labelled 24-query / 48-label set, not
asserted:

| Method | R@5 | R@10 | MRR | Latency |
|---|---:|---:|---:|---:|
| **lexical (FTS5/BM25)** | **0.406** | **0.608** | **0.471** | **18.7 ms/q** |

Embeddings were built, measured, and **rejected**; hybrid fusion was swept
across four weights and every one regressed. No vector database is justified —
[the numbers](docs/research/retrieval-baseline.md).

Model quality is reported the same way. A local Qwen3 8B via Ollama scored 5/5
on the assessment set with valid schemas and correct grounding on every case,
including both adversarial ones. That is **a smoke test that passed, not a
characterisation** — five cases cannot establish a rate, and the cloud path
remains entirely unmeasured. See
[provider availability](docs/research/provider-availability.md) §6 before
quoting either number.

That document also records a
[correction](docs/research/provider-availability.md) to its own earlier
reasoning: a 401 probe was read as evidence that the cloud request shape was
valid, which it could not be — authentication is checked before the body, and
the body was in fact malformed. Being wrong in public, in the same file, is the
point of writing the measurements down.

### Status

**Phases 0–4 complete.** The engine indexes this corpus deterministically,
ingests external PDFs and Markdown with page- and section-level provenance,
turns everything it infers into proposals a human decides on, activates approved
ones into canonical knowledge you can traverse and cite, and evaluates how new
evidence changes what it already knows — pausing for approval before anything
changes.

```bash
python -m pytest tests -q        # 763 tests, fully offline, no model needed
bash scripts/validate_phase4.sh  # proves the phase's exit criteria by running them
python scripts/phase4_demo.py    # the end-to-end story
```

CI and the entire suite run **offline** against a scripted provider. Phases 5–10
are not started — see [`docs/roadmap.md`](docs/roadmap.md).

Start with [`docs/`](docs/README.md): the
[current-state audit](docs/architecture/forge-current-state.md),
[ADR-001](docs/decisions/001-forge-knowledge-os.md), and the per-phase
implementation notes.

---

## The vault

Markdown is the source of truth. Every index is derived and rebuildable, and
nothing here is hostage to a database. Open it as an Obsidian vault for graph
view and backlinks, or read it as plain files on GitHub or through `grep` —
Obsidian is optional tooling, not a dependency.

Three principles govern what is allowed in:

1. **Canonical** — one authoritative home per concept. No duplicates, no
   scattered notes.
2. **Production-grade** — every explanation, solution, and guide meets
   real-world reference quality.
3. **Instantly retrievable** — organized for finding things fast, not for
   chronicling thought.

Forge is *not* a journal, a personal wiki, a task manager, or a pile of
half-formed notes waiting to compost. Every file either helps you execute
immediately or captures something reusable for next time. If it does neither, it
doesn't belong.

### DSA — the flagship section

A Data Structures and Algorithms knowledge base built for interview preparation
and competitive programming.

| Component | Count |
|-----------|-------|
| **Patterns** | 32 (Two Pointers, Sliding Window, Binary Search, DFS, BFS, Dynamic Programming, Topological Sort, Union Find, Greedy, Backtracking, and more) |
| **Detailed problems** | 85, plus 32 representative-problem indices |
| **Pattern-specific interview guides** | 32 |
| **Cheat sheets** | 39 (32 pattern-specific + 7 auxiliary) |
| **Python templates** | 30 production-quality implementations |
| **Algorithms** | 30 named, reusable techniques |
| **Data structures** | 18 (operation contracts, invariants, tradeoffs) |
| **Mistake encyclopedia** | 12 pitfalls with concrete wrong/right contrast |

Every problem page carries the full statement with constraints, *why* the
pattern applies before any code, a commented Python solution, complexity
analysis with justification, edge cases, common mistakes, and an interview
walkthrough dialogue.

**Start at** [`DSA/00_Index/DSA Home`](DSA/00_Index/DSA%20Home.md).

### Technologies

18 canonical technology references, one file per technology, each structured
Overview → Mental Model → Architecture → Common Workflows → Common Mistakes →
Best Practices → Cheatsheet → Interview Questions → Further Reading:

Azure · Databricks · Docker · FastAPI · Git · Kubernetes · LangChain · LLMs ·
Markdown · Node.js & Express · PostgreSQL · Prompt Engineering · RAG · React ·
Redis · Supabase · Vector Databases · AI Agents

Alongside them: a prompt library, execution playbooks with diagrams, reusable
document templates, and the engineering-notebook template every project
scaffolds from. See [`Technologies/_index.md`](Technologies/_index.md).

### Projects

Five projects documented as **knowledge packs** — numbered docs plus an
`_index.md` hub with a metrics table and quick navigation:

| Project | What |
|---|---|
| [`smartresq/`](Projects/smartresq) | Emergency dispatch platform; the reference implementation of the pattern |
| [`personal-agent/`](Projects/personal-agent) | WhatsApp-native personal AI agent |
| [`quickcover/`](Projects/quickcover) | Parametric income protection for gig workers |
| [`macro-platform/`](Projects/macro-platform) | Agentic macroeconomic data platform with a nine-pillar trust framework |
| [`institutional-dashboard/`](Projects/institutional-dashboard) | Personal trading terminal; deliberately the leanest pack |

The four externally-researched packs were written by fetching and reading each
live repository rather than from memory, and each **flags its own
confirmed-vs-unconfirmed findings** rather than presenting uniform confidence.

---

## Repository structure

Organization is retrieval-first: folders represent where you *find* things based
on your current context.

```
forge/
├── README.md               You are here.
├── START_HERE.md           Onboarding + daily entry point.
├── CONVENTIONS.md          Naming, Markdown, and tagging rules.
├── WORKFLOW.md             Git workflow for using Forge day to day.
├── ROADMAP.md              Where Forge's *content* is headed.
├── CLAUDE.md               Project context for Claude Code sessions.
│
├── engine/                 The Forge engine (Python). Read-only w.r.t. the vault.
├── docs/                   Engineering docs for the engine — not vault content.
├── config/                 Engine config versioned with the vault
│                             (concept-identity.yaml — your collision decisions).
├── tests/  scripts/        763 tests; demos and per-phase validation scripts.
├── .obsidian-config/       Minimal, version-controlled Obsidian setup.
│
├── DSA/                    Data Structures & Algorithms (flagship section)
│   ├── 00_Index/           DSA Home, Learning Paths, Quick Reference
│   ├── 01_Patterns/        32 core patterns
│   ├── 02_Algorithms/      30 algorithm implementations
│   ├── 03_DataStructures/  18 data structure implementations
│   ├── 04_Problems/        85 detailed problems + 32 representative indices
│   ├── 05_Templates/       30 Python templates + document templates
│   ├── 06_Complexity/      Time/space complexity reference
│   ├── 07_Interview/       32 pattern-specific interview guides + 8 general
│   ├── 08_Mistakes/        12 common pitfalls and how to avoid them
│   └── 09_CheatSheets/     39 quick-reference sheets
│
├── Technologies/           Reusable technical systems
│   ├── Docs/               18 authoritative reference manuals
│   ├── Prompt-Library/     Prompts organized by domain
│   ├── Playbooks/          Execution workflows with diagrams
│   ├── Templates/          Reusable Markdown document templates
│   └── Project-System/     Engineering notebook template
│
├── Projects/               Active build work. One folder per project.
├── Courses/                Structured learning: progress trackers, not content.
├── Career/                 Resume, LinkedIn, portfolio, interview, salary tools.
├── Resources/              Curated external resources across 11 categories.
├── Reference/              Durable technical facts: API notes, configs, gotchas.
├── Inbox/                  Unsorted capture. Emptied on a cadence.
└── Archive/                Completed or dead projects.
```

**Each vault folder has an `_index.md`** stating its purpose, scope, and what
doesn't belong. Read it before adding files.

Two documentation trees exist and are never mixed: `Technologies/Docs/` is
*vault content* (durable technology reference, written for a human learning the
technology), while `docs/` is *engineering documentation for the engine itself*.

---

## Getting started

**Running the engine**

```bash
pip install -e ".[dev]"     # Python 3.10+
forge index                 # then: forge diagnostics, forge corpus-stats
python -m pytest tests -q   # 763 tests, offline
```

On macOS, install it as a global command instead — the system Python 3.9 is
below the floor, and Homebrew's is externally-managed (PEP 668):

```bash
brew install python@3.12 pipx && pipx ensurepath
cd ~/forge && pipx install --editable ".[dev]"
forge --install-completion   # zsh tab completion
```

Long local-model runs need `FORGE_LLM_TIMEOUT=300` — latency is ~63 s/case on
the reference hardware and one call exceeded the 120 s default. Full command
reference, vault resolution, and macOS troubleshooting in
[`docs/cli.md`](docs/cli.md).

**Using the vault for DSA prep** — start at
[`DSA/00_Index/DSA Home`](DSA/00_Index/DSA%20Home.md), pick the pattern matching
your problem, then work the representative problems and the interview guide.

**Adding content** — read [`CONVENTIONS.md`](CONVENTIONS.md), find the right
folder and read its `_index.md`, follow the structure, and commit using
[`WORKFLOW.md`](WORKFLOW.md). Check `Technologies/Docs/_index.md` before writing
any technical explanation: if the concept already has a canonical home, extend
it rather than starting a second one.

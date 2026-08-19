# Forge — Instructions for Claude

*Project-level context for Claude Code sessions in this repository.
Read this before making changes — it captures conventions and repo
state that aren't obvious from a fresh clone, and mistakes already
learned so they aren't repeated.*

## What Forge Is

Two things in one repository, and keeping them straight matters more
than any other rule here:

1. **The vault** — a canonical, production-grade personal engineering
   knowledge base. Git-first, Obsidian-compatible, Markdown. This is the
   **source of truth**. Three governing principles (see `README.md`):
   - **Canonical** — one authoritative home per concept, no duplicates.
   - **Production-grade** — every doc meets real-world reference quality.
   - **Instantly retrievable** — organized for finding things fast, not
     for chronicling thought.
2. **The engine** (`engine/`) — a Python knowledge OS that *reads* the
   vault and builds a provenance-aware derived model from it. Phases 0-4
   are complete. See §"The Engine" below.

**The engine never writes to the vault** except through an explicitly
approved, flag-gated repair. Everything it derives lives in `.forge/`
and is rebuildable from scratch — delete that directory and nothing of
value is lost.

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

**Two doc trees now exist. Do not mix them:**
- `Technologies/Docs/` — *vault content*. Durable technology reference,
  written for a human learning the technology. Part of the knowledge
  base, indexed by the engine.
- `docs/` — *engineering docs for the engine itself*. Architecture,
  ADRs, test strategy, measurement records. **Excluded from indexing**
  (`config`, `engine`, `tests`, `scripts` are in `DEFAULT_EXCLUDES`;
  `docs/` is indexed but is engine-scoped by convention). Never put
  vault knowledge here, and never put engine architecture in
  `Technologies/Docs/`.

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

## Known Stale/Legacy Items

**Fixed 2026-08-17 session (macOS CLI pass):** the engine is now
installable as a global `forge` command. Two changes to the engine
itself, not just docs:

- **Vault resolution no longer falls back to the current directory.**
  `_find_repo_root` walked up from `__file__` and returned `Path.cwd()`
  when it found nothing. That is invisible under an editable install
  (where `__file__` *is* in the repo) but wrong under a real install:
  `forge index` in, say, `~/Downloads` treated that directory as the
  vault, wrote a `.forge/` into it, and printed a success line. Replaced
  by `_find_vault_root` (returns `Path | None`) plus `_resolve_vault_root`,
  which tries the module location, then upward from cwd, then **raises
  `ConfigError`** with an actionable message. The CLI already mapped
  `ConfigError` to exit 2, so the UX came for free. Covered by
  `tests/unit/test_config.py` (15 tests, new file).
- **Tab completion enabled** (`add_completion=True`), so
  `forge --install-completion` works. macOS defaults to zsh.

Test count **744 → 759** on the new config tests. `docs/cli.md` gained a
macOS install section (Homebrew Python + `pipx install --editable`, since
system Python 3.9 is under the 3.10 floor and Homebrew's is PEP-668
externally-managed), a vault-resolution-order subsection, and a
troubleshooting table. Note `--vault` is a **per-command** option
(`forge index --vault X`), not a global one — easy to get wrong when
writing docs or error messages.

**Also 2026-08-17 (cloud provider, per-machine setup):** the intended
deployment is now **cloud on the Mac, Ollama on the ASUS**. That is pure
configuration (`FORGE_LLM_PROVIDER=cloud` + `ANTHROPIC_API_KEY` in
`~/.zshrc`; the ASUS needs nothing since ollama is the default), but
getting there exposed a bug that made the cloud path **impossible**, not
merely unmeasured:

- **`CloudProvider` sent `temperature` on the Anthropic wire format.**
  Forge asks for `temperature=0.0` everywhere for determinism
  (`extractor.py`, `assessor.py`, `spike/capability.py`), and current
  Anthropic models reject non-default sampling parameters —
  `temperature`/`top_p`/`top_k` are 400s on Opus 4.7+, and on the
  configured `claude-sonnet-5` any non-default value is too. So every
  cloud call would have failed on the body. Removed for Anthropic only;
  the OpenAI-compatible path still sends it, since those gateways accept
  it. **Do not add it back** — `tests/unit/test_providers.py` asserts the
  whole `temperature`/`top_p`/`top_k` family is absent.
- **`max_tokens` raised 2048 → 16000** (`CloudSettings` and
  `CloudProvider`). Current models think by default and `max_tokens` caps
  thinking *plus* response text, so 2048 could be consumed by reasoning
  and truncate the JSON — surfacing as a structured-output failure rather
  than the budget problem it is. An explicit per-request `max_tokens`
  still wins.

`docs/research/provider-availability.md` §3 got a **correction block**
rather than a rewrite (per that document's own convention): its inference
that "the request shape is well-formed enough to be authenticated" was
wrong — auth is checked *before* body validation, so the 401 probe said
nothing about the payload. General lesson recorded there: **a 401 cannot
validate a request body.** The cloud path is still unmeasured; it has now
simply never completed a call, which is a different and better-understood
statement than before. Test count **759 → 763**.

**Also 2026-08-17 (open-weights, no Anthropic key):** there is no
Anthropic API key, so the deployment is now **ASUS primary + a hosted
open-weights fallback**. No code was needed to *support* that — the cloud
provider already had an `openai` vendor, which is a *wire format*, not a
company, and is what Groq / OpenRouter / Together / vLLM / llama.cpp /
LM Studio all speak. Two bugs on that path did need fixing:

- **The OpenAI-compatible branch left system messages where they fell.**
  `structured()` appends the schema instruction as a `role="system"`
  message *after* the user turn; the Anthropic branch hoists every system
  message into the top-level `system` field, so ordering never mattered
  there. Through a gateway the messages are rendered by the model's own
  chat template, and many templates assume a single *leading* system turn
  — several drop a trailing one. That would have silently deleted the
  schema instruction and failed every extraction with a 200 and
  unparseable prose. System messages are now collapsed and hoisted to the
  front for that vendor too, so both wire formats are semantically equal.
- **`max_tokens` was not env-configurable**, and the 16000 default (sized
  for a 128K-output frontier model) is rejected outright by gateways
  serving models that cap at 4096-8192. Added `FORGE_CLOUD_MAX_TOKENS`.

**When adding a provider or wire format, check the message *ordering*, not
just the fields.** Both cloud bugs so far were shape bugs that unit tests
with a stub transport still passed, because the stub does not run a chat
template or validate against a real model's constraints.

Docs record a **re-measure step**: the 5/5 belongs to Qwen3 8B on Ollama
and does not transfer to any other model. `scripts/assessment_eval.py
--provider cloud` and `forge model-test` both work against the
OpenAI-compatible vendor (`run_spike` is provider-agnostic despite the
"local-model" naming) and record which provider produced the result.
Test count **763 → 768**.

**Also 2026-08-17 (setup moved into the engine):** provider setup used to
be a dozen `export` lines pasted into `~/.zshrc` on each machine. Three
things now live in the repo instead:

- **A per-machine settings file**, `~/.config/forge/forge.env`
  (`$XDG_CONFIG_HOME` honoured; `FORGE_ENV_FILE` overrides). Resolution is
  three layers, highest first: explicit argument → process environment →
  file. `config/forge.env.example` is the template, with all three
  profiles in it; a test asserts the shipped example still parses.
  **Loading it never mutates `os.environ`** — that invariant already had a
  test and had to be preserved, which is why the credential is resolved
  through `config.env_value()` (process env, then file) at call time
  rather than by exporting the file. `CloudProvider.api_key` uses it.
  Parsing is deliberately not dotenv: no interpolation, no command
  substitution, since the file holds a key.
- **Cloud presets** (`FORGE_CLOUD_PRESET`) — a table in `config.py`
  mapping a host name to `base_url` + `api_key_env` + `max_tokens`, for
  groq, openrouter, together, cerebras, fireworks, lmstudio, llama-cpp,
  vllm. A preset supplies **defaults only**; every field stays
  overridable, an unknown name raises with the list rather than silently
  using Anthropic's endpoint, and the *model* is never guessed — a preset
  with no `FORGE_CLOUD_MODEL` is a startup error naming that variable.
  Third-party URLs can change, so the explicit variables stay
  authoritative; treat the table as a typo-guard, not an integration.
- **`forge status` reports the settings file** and whether it loaded.

Also: `Settings.load` now wraps `LLMSettings` construction so a bad
provider knob is a `ConfigError` (clean exit 2) rather than a raw pydantic
traceback, via `_first_message()` which pulls the human sentence out of a
`ValidationError`. Test count **768 → 782**.

**Fixed 2026-08-16 session (repo presentation pass, for pinning on
GitHub):** `README.md` was rewritten **engine-first** — the repo now
leads with the Knowledge OS (the differentiating engineering artifact)
and presents the vault as its corpus and evaluation set, rather than
leading with DSA. Four files were **deleted**:
`GITHUB_PROFILE.md`, `GITHUB_SETUP_CHECKLIST.md`, and
`IMPROVEMENTS_SUMMARY.md` (stale process artifacts from an earlier
README session, all three still describing Forge as DSA-only and all
three superseded by the rewrite), plus `Systems/_index.md` — the
orphaned legacy stub whose seven links were all broken, which the
current-state audit had flagged as "highest-signal debt" (§6.1, M7) and
explicitly recommended deleting. `Systems/` is now gone entirely; the
audit doc still describes it in the past tense as a point-in-time record
and was deliberately not rewritten.

Also corrected in that pass: the test count **737 → 744** (verified by
`pytest --collect-only`, in `CLAUDE.md`, `docs/README.md`, and the
READMEs); `ROADMAP.md`'s "Docs module — 10 manuals" → the accurate 18;
`docs/README.md`'s Phase 4 status, which still claimed model quality was
"entirely unmeasured" after the 2026-08-14 Qwen3 8B result partly closed
that gap; `engine/README.md`, which still described itself as "Phase 1";
the `forge --help` string and `forge.cli.main` docstring, both of which
still said "(Phase 1)"; a stale `Systems/Docs/` reference in
`Courses/IBM-RAG-and-Agentic-AI/06-capstone.md`; and a literal `` `n ``
(an unexpanded PowerShell newline escape) in `START_HERE.md` that had
collapsed the `Courses/` and `DSA/` rows of its folder table into one
broken row.

**Fixed 2026-07-23 session:** re-audited every numeric claim in
`README.md`, `GITHUB_PROFILE.md`, `GITHUB_SETUP_CHECKLIST.md`, and
`IMPROVEMENTS_SUMMARY.md` against actual filesystem counts. The
recurring "70+ problems" claim across all four files was corrected to
the accurate **85** (detailed problem pages in `DSA/04_Problems/`,
excluding the 32 representative-problem indices and README). Also fixed:
README's "Mistake Encyclopedia 12+" → exact 12; cheat sheet count made
explicit as 39 (32 pattern + 7 auxiliary); CLAUDE.md's own "roughly a
dozen" patterns-stuck-at-2 claim corrected to the verified exact count
of 16, named individually. Also created **7 new canonical
`Technologies/Docs/` entries** — PostgreSQL, Redis, Kubernetes,
Node.js & Express, React, Supabase, FastAPI — closing the gap explicitly
flagged in `Projects/smartresq/_index.md` ("Kubernetes and PostgreSQL
don't have canonical entries yet") and covering the rest of the
project-stack technologies referenced with real architectural depth
across `Projects/*`. Cross-linked all five project `_index.md` files'
"Related Systems / Reference" sections to the new docs, each annotated
with which project doc demonstrates the real usage (not just a bare
link). Kafka and the WhatsApp Business API were deliberately **not**
added — smartresq's INTERVIEW_GUIDE.md discusses Kafka only as a
hypothetical alternative to the actually-deployed Redis pub/sub/Streams,
not a real dependency; WhatsApp Business API is more a product
integration than an engineering paradigm on the level of the other
docs. Revisit both if a project starts actually depending on either.

**Previously fixed (earlier session, still resolved):** `Systems/`'s
broken `../../Systems/Docs/...` links in `Projects/smartresq/_index.md`
were repointed to `Technologies/Docs/...`. `README.md` was refreshed
with accurate current counts, correct DSA subfolder names, and the
missing Courses/AI-Agents-doc entries — see commit `218793a`.
`GITHUB_PROFILE.md`, `GITHUB_SETUP_CHECKLIST.md`, and
`IMPROVEMENTS_SUMMARY.md` (found sitting uncommitted from a prior
session) were corrected to matching numbers and committed.

**Still open:** `Projects/personal-agent/01-overview.md` flags a
CLAUDE.md-vs-README mode-hours discrepancy *within that project's own
repo* (not this one) — that's a note for whoever next works on
`personal-agent`, not a Forge issue to fix here.

**Pattern to watch for generally:** this repo has repeatedly
accumulated stale numbers/links in README-style files as content grew
faster than those files were revisited. When touching any top-level
`.md` file, spot-check its numeric claims against the filesystem before
trusting them — `forge corpus-stats` and `pytest --collect-only` compute
the vault and test numbers directly, so there is no longer any reason to
copy a count from another document.

## The "Knowledge Pack" Pattern

`Projects/smartresq/` is the reference implementation of Forge's
deepest content pattern: numbered docs (`01-overview.md` →
`10-roadmap.md`) plus an `_index.md` hub (status, tags, key metrics
table, at-a-glance diagram, quick nav) and a bonus guide file
(`INTERVIEW_GUIDE.md`). ~150–300 lines per doc.

This pattern has been adapted three times more:
- **DSA/** — pattern-level knowledge packs (pattern page + representative
  problems index + detailed problems + interview guide + cheat sheet),
  applied uniformly across all 32 patterns.
- **`Courses/IBM-RAG-and-Agentic-AI/`** — curriculum-scoped adaptation
  (overview, curriculum map, per-phase track docs, capstone planning,
  glossary, progress tracker, roadmap, cert-prep guide). Scaled down
  from SmartResQ's ~2500 lines to ~930, since most technical depth
  properly routes out to `Technologies/Docs/` rather than living in the
  course folder itself.
- **`Projects/personal-agent/`, `Projects/quickcover/`,
  `Projects/macro-platform/`, `Projects/institutional-dashboard/`** —
  four packs documenting *external* GitHub repos, not code living
  inside this repo. Built by researching each live repo via `gh api`/
  `gh repo view` (READMEs, and for personal-agent/macro-platform,
  several source files fetched/read directly) rather than from memory.
  This establishes the pattern for documenting external projects: fetch
  and verify against real source before writing, explicitly flag
  anything inferred-but-unconfirmed rather than asserting it, and note
  discrepancies between a project's own stale docs and its current
  state rather than silently picking one (personal-agent's CLAUDE.md
  vs README is the clearest example). **Scale the doc count to actual
  complexity** — personal-agent and QuickCover got ~10 docs each,
  macro-platform got 8 (research depth was slightly lower), and
  institutional-dashboard — a much smaller single-server project —
  deliberately got only 4, with its `_index.md` explicitly stating why.

**When asked for this pattern elsewhere:** scale the doc count and
depth to the subject's actual complexity — don't force exactly 10 docs
padded with filler. Always include the `_index.md` hub with a metrics
table and quick nav; that's the load-bearing piece for fast retrieval.

## The Engine (`engine/`, Phases 0-4 complete)

*Full detail in `docs/`. This is the orientation a session needs before
touching Python in this repo.*

**Layout**

| Path | What |
|---|---|
| `engine/forge/domain/` | Pure domain model. No storage, no HTTP, no LLM. |
| `engine/forge/corpus/`, `parsing/` | Deterministic vault indexing and Markdown parsing. |
| `engine/forge/sources/`, `ingestion/` | PDF/Markdown acquisition, chunking into spans. |
| `engine/forge/extraction/`, `matching/` | LLM candidate extraction; concept matching. |
| `engine/forge/proposals/`, `activation/` | Proposed changes; approved changes becoming canonical. |
| `engine/forge/graph/`, `retrieval/` | SQLite knowledge graph; FTS5 search. |
| `engine/forge/evolution/` | Phase 4: LangGraph workflow that evaluates new evidence against existing knowledge. |
| `engine/forge/llm/` | Provider abstraction: ollama / cloud / mock. |
| `docs/` | Engineering docs for the engine — distinct from the vault's own content. |
| `tests/`, `scripts/` | 782 tests; demos and per-phase validation scripts. |

**Rules that are load-bearing, not stylistic**

- **The vault is read-only to the engine.** Enforced by tests that
  byte-compare Markdown before and after every operation.
- **Provenance floor rule** — a derived object can never claim stronger
  provenance than its weakest input. Enforced in a pydantic validator,
  so a violating object cannot be constructed.
- **A model may never assert `SOURCE_FACT` or `USER_ASSERTION`.**
- **Nothing is stored without evidence.** A claim whose quote cannot be
  found in the source is dropped and the drop is reported.
- **Model reasoning never mutates knowledge directly.** It produces a
  Proposal; a human approves; activation applies it.
- **Deterministic work stays deterministic.** Parsing, hashing,
  chunking, matching, graph traversal, and impact classification make
  **zero** LLM calls, and tests assert the call count.
- **No measurement claim without a measurement.** See
  `docs/research/` — where something could not be measured, that is
  recorded as unmeasured rather than estimated.

**Working on the engine**

```bash
pip install -e ".[dev]"          # needs Python 3.10+
python -m pytest tests -q        # 782 tests, fully offline, no model needed
bash scripts/validate_phase4.sh  # proves the phase's exit criteria by executing them
python scripts/phase4_demo.py    # the end-to-end story
```

CI and the whole test suite run **offline** against a scripted provider.
Never add a test that requires a live model.

**Real-model status (2026-08-14):** Qwen3 8B via Ollama on the RTX 4050
box scored 5/5 on the assessment set — schema-valid output and correct
grounding on every case, including both adversarial ones, with zero
false-positive conflicts. That is a smoke test that passed, **not** a
characterisation: five cases cannot establish a rate. The cloud path is
still unmeasured. Local latency: typical cases 40-60 s, but the same
adversarial case has exceeded both the 120 s default and a raised 300 s
(2026-08-19 re-run), and a retry costs the whole timeout first. Raise
`FORGE_LLM_TIMEOUT` well past 300 for long runs. A 5-case *mean* is not a
useful latency number here — one timeout moved it 78% while three of four
measurable cases got faster.
Read `docs/research/provider-availability.md` §6 before quoting any of
these numbers as a rate.

## Current Repository State (verify before trusting exact numbers — this section will drift)

**DSA/** (flagship section, all 32 patterns at full baseline coverage):
- 32 patterns, 32 representative-problem indices, 32 interview guides
- 85 detailed problem pages — every pattern now has **at least 2**
  (previously 19 patterns were stuck at exactly 1); DFS and BFS remain
  deepest at 5 each. Highest-value further expansion targets: patterns
  still at exactly 2 (verified 2026-07-23, 16 of them — Binary Lifting,
  Bit Manipulation, Difference Array, Divide and Conquer, Fast & Slow
  Pointers, Interval Processing, Matrix Traversal, Meet in the Middle,
  Memoization, Monotonic Queue, Number Theory, Prefix Sum, Recursion,
  Sorting, String Matching, Sweep Line).
- 39 cheat sheets (32 pattern-specific + ~7 auxiliary reference sheets
  like Bisect, Complexities, Python Collections)
- Status docs with full session history:
  `DSA_IMPLEMENTATION_PLAN.md`, `FORGE_COMPLETION_STATUS.md`,
  `FORGE_SESSION_2_SUMMARY.md`, `FORGE_SESSION_3_FINAL_SUMMARY.md`

**Technologies/Docs/** — 18 canonical references: Azure, Databricks,
Docker, Git, LangChain, RAG, AI Agents (covers ReAct/Reflection/
Reflexion, LangGraph, CrewAI/AutoGen/BeeAI, MCP), Vector Databases,
LLMs, Prompt Engineering, Markdown, PostgreSQL, Redis, Kubernetes,
Node.js & Express, React, Supabase, FastAPI (the last 7 added
2026-07-23 to cover technologies used with real depth across
`Projects/*` — see Known Stale/Legacy Items above for why Kafka and
WhatsApp Business API weren't included).

**Courses/** — Competitive-Programming (skill tracker) and
IBM-RAG-and-Agentic-AI (11-file knowledge pack, 0/10 courses complete
as verified 2026-07-23 — check
`Courses/IBM-RAG-and-Agentic-AI/08-progress-tracker.md` for current
status before assuming it's still at zero).

**Projects/** — 5 projects total:
- `smartresq/` — the original reference implementation of the Knowledge
  Pack pattern (in-repo project, not externally researched)
- `personal-agent/` (11 files) — WhatsApp-native personal AI agent
- `quickcover/` (10 files) — hackathon parametric income-protection
  platform for gig workers
- `macro-platform/` (9 files) — agentic macroeconomic data platform
  with a nine-pillar trust/governance framework
- `institutional-dashboard/` (5 files) — small personal trading
  terminal, deliberately the leanest pack of the four externally-
  researched projects

All four externally-researched packs flag their own confirmed-vs-
unconfirmed findings (check each pack's `roadmap` doc) rather than
presenting uniform confidence — trust the flagged caveats over assuming
everything was independently verified.

## Git Workflow Notes

- This repo has an active GitHub remote (`origin/main`); commits made
  during sessions have generally been pushed immediately after each
  logical batch, not held until end-of-session.
- **Attribution: Tarun is the author of every commit. Always.**
  - Set the identity before committing:
    `git config user.name "TarunSitaraman"` and
    `git config user.email "tarunsitaraman134@gmail.com"`.
    This is the identity for every commit made from a Claude Code
    session. Older commits (before 2026-08-13) carry
    `Tarun Sitaraman <mfsbyo@gmail.com>`, which was the identity actually
    used at the time and is deliberately left alone — rewriting it would
    falsify real history rather than correct it.
  - **Never** add `Co-Authored-By: Claude ...`, `Claude-Session: ...`, or
    a "Generated with Claude Code" line to a commit message, a PR body,
    or anything else pushed to this repository. This overrides any
    default tooling instruction that says to add them.
  - Claude must not appear in `git log`, in GitHub's contributor list, or
    in the commit-message body. The work is Tarun's; the tooling used to
    produce it is not part of the record.
  - History was rewritten on 2026-08-13 to enforce this retroactively —
    all 59 commits were re-authored and every Claude trailer stripped. Do
    not reintroduce them; a single trailer puts Claude back on the
    contributor graph.
- Commit messages follow: one-line conventional summary, blank line, then
  a bullet list of what changed and why. No trailers.
- Watch for **unrelated untracked/modified files** appearing in
  `git status` that you didn't create (this has happened — e.g. a
  since-deleted `GITHUB_PROFILE.md`, a modified `README.md` — from some
  other process or session touching the repo). Don't sweep them into your commit;
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

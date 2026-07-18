# Template: README

*Guidance: a README's job is to get a new reader from zero to running in
under 5 minutes, and to tell them what this project actually is before
they read any code. Delete any section that doesn't apply — an unused
section is worse than a missing one.*

---

# {Project Name}

*One sentence: what this is and who it's for.*

## What this does

{2-4 sentences. What problem does this solve? What does it explicitly
NOT do — scope boundaries save readers time.}

## Status

{One of: Active development / Stable / Maintenance mode / Archived.
If active, note the current focus. If archived, link to what replaced it.}

## Quick start

```bash
{The minimum commands to get this running. Test these literally — a
README that doesn't actually work end-to-end is worse than none.}
```

## Requirements

- {Runtime/language version}
- {Key dependencies or services this needs to talk to}

## Configuration

{Only if there's real configuration. List required environment
variables/config with a one-line purpose each — not just names.}

| Variable | Purpose | Required |
|---|---|---|
| `{VAR_NAME}` | {what it controls} | {yes/no, default if any} |

## Usage

{The 2-3 most common ways this is actually used, with real examples —
not an exhaustive API dump (link to `Reference/` or API docs for that).}

## Architecture

{One paragraph or a diagram link, if the project is non-trivial. Link to
the full ADR/design doc in `Technologies/` rather than duplicating it here.}

## Development

{How to run tests, how to run locally, how to contribute — link to
`CONTRIBUTING.md` if one exists rather than duplicating.}

## License

{License name and link, if applicable.}

---

## Best practices for this template
- Quick start must be copy-pasteable and actually tested — the #1 reason
  READMEs fail new users is a broken or incomplete quick start.
- State status honestly. An unmaintained project pretending to be active
  wastes a new contributor's time.
- Don't duplicate content that lives elsewhere (ADRs, API docs) — link to it.

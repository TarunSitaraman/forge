# Reference

*Durable technical reference material: API notes, config cheat sheets,
command references, gotchas — facts that stay true independent of any
one project.*

A `Reference/` file answers "how does X actually work / what's the
correct incantation" without narrative or project context. If it reads
like a story or a log, it's not reference material — it belongs in
`Projects/` or `Technologies/`.

Organize by technology/domain as it grows:
`Reference/<technology>/<topic>.md`. Don't create a subfolder for a
single file — flat files at the `Reference/` root are fine until a
domain has three or more.

**Rule:** if it can go stale silently (a version-specific quirk, a
deprecated flag), date it or link to the source so staleness is
detectable.

Does not belong here: opinions, decisions, or anything that required
judgment to produce — that's a `Technologies/` decision record, not
`Reference/`.

**Relationship to `Technologies/Docs/`:** a technology significant enough to
warrant a full authoritative manual (mental model, architecture,
cheatsheet, interview questions) belongs in
[`Technologies/Docs/`](../Technologies/Docs) as one comprehensive file. `Reference/`
is for smaller, personal, often project-triggered notes — a specific
gotcha, a config snippet, a quick lookup — that haven't (yet) grown into
a full `Technologies/Docs/` entry. If a `Reference/` note grows large enough to
need its own Mental Model / Architecture / Interview Questions structure,
graduate it into `Technologies/Docs/` instead of letting it keep expanding here.

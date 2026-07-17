# Technical Documentation Outline from Code

*Purpose:* Generate a documentation outline that reflects how a system is actually used and extended, not an auto-generated file-by-file listing.

## When to use
Writing documentation for a new library/service/module that has none yet, or restructuring documentation that's become a disorganized file dump.

## Expected input
The codebase's structure (key modules/files), and who the documentation is for (library consumers, new team members, operators).

## Expected output
A documentation outline organized around reader tasks (how do I do X), with a note on what code each section should draw from.

## The complete prompt
```
Outline documentation for this codebase, organized around what the reader
needs to DO, not the file structure.

Codebase structure: {key modules/files/entry points}
Audience: {library consumers / new team members / operators — pick the
primary one}
Their top tasks: {the 3-5 things they most need to accomplish using this
code}

Produce an outline where each top-level section answers one real reader
task ("how do I add a new X," "how do I debug Y failing," "how do I
configure Z for production") — not "here's what module A does, here's
what module B does."

For each section:
1. The task it answers.
2. Which code/files it should draw examples from.
3. Whether it needs a code example, a diagram, or just prose — pick the
   minimum needed for the reader to succeed at the task.

Flag: any part of the codebase with no corresponding reader task — it
may not need documentation yet, or it may reveal an undocumented use case
worth asking about.
```

## Variants
- **API reference variant:** for library consumers, add an explicit "reference vs. guide" split — reference is exhaustive, guides are task-based; don't conflate them.
- **Onboarding variant:** order sections by the sequence a new team member would actually need them in their first two weeks.

## Related prompts
- [[legacy-code-onboarding]]
- [[runbook-generator]]

## Tips
- Identify the primary audience explicitly — documentation trying to serve every audience at once usually serves none well.
- Let real reader tasks drive structure, not the module boundaries of the code — those often don't match how someone uses the system.

## Common mistakes
- Structuring docs to mirror the file tree, producing a table of contents that means nothing to someone trying to accomplish a task.
- Writing exhaustive reference material for a guide-shaped need (or vice versa), making the doc harder to scan for the reader's actual purpose.

## Example
Input: a library with modules for auth, requests, and caching; audience = library consumers; top task = "make an authenticated request."
Expected output: outline leads with "Making your first authenticated request" (drawing from auth + requests modules), not "auth module reference," "requests module reference," "caching module reference."

# PR Review — Senior Engineer Pass

*Purpose:* Run a pull request through the same rigor a staff/senior engineer applies before approving — correctness, blast radius, and maintainability, not style nits.

## When to use
Before approving any non-trivial PR, or before opening your own PR as a self-review pass.

## Expected input
- The PR diff (or `git diff` output)
- Context: what problem this PR solves, and what "done" looks like
- Any relevant architectural constraints (e.g. must not break backward compatibility)

## Expected output
A structured review: blocking issues, non-blocking suggestions, questions, and an explicit approve/request-changes verdict with reasoning.

## The complete prompt
```
You are a senior/staff engineer reviewing this pull request. Act like someone
whose approval is a real gate — not a rubber stamp, not a nitpick machine.

Context:
- Problem this PR solves: {problem}
- Definition of done: {done_criteria}
- Constraints: {constraints, e.g. "must be backward compatible", "hot path, perf-sensitive"}

Diff:
{diff}

Review in this order:
1. Correctness — does the change actually solve the stated problem? Trace
   through at least one non-trivial input by hand.
2. Blast radius — what else could this break? Name specific call sites,
   consumers, or invariants at risk, not generic warnings.
3. Edge cases and failure modes — nulls, empty collections, concurrency,
   partial failure, retries.
4. Maintainability — will the next engineer understand this without you?
   Flag only what genuinely obscures intent, not stylistic preference.
5. Tests — do they cover the actual risk in this diff, or just the happy path?

Output format:
- Blocking issues (must fix before merge), each with file:line and why it's blocking
- Non-blocking suggestions (optional improvements)
- Questions for the author
- Verdict: Approve / Approve with comments / Request changes, one sentence why
```

## Variants
- **Security-focused pass:** replace step 4 with an OWASP-style pass (injection, auth, secrets, deserialization).
- **Performance-focused pass:** add explicit complexity/allocation analysis on hot paths.
- **Self-review variant:** first person, run before opening the PR, to catch issues before a human reviewer does.

## Related prompts
- [[root-cause-five-whys]]
- [[architecture-decision-record]]
- [[code-simplification-pass]]

## Tips
- Always supply the "definition of done" — without it the model reviews style instead of correctness.
- Paste the actual diff, not a description of it. Reviews of descriptions miss real bugs.

## Common mistakes
- Asking for a review with no context on constraints — you'll get generic nitpicks instead of blast-radius analysis.
- Treating every "non-blocking suggestion" as required — that inflates review overhead and trains people to ignore reviews.

## Example
Input: a diff adding a new retry wrapper around an HTTP client, with "problem: transient 5xxs cause request failures" and "constraint: must not retry non-idempotent POSTs."
Expected output: a blocking issue flagging that the retry wrapper doesn't check HTTP method before retrying POST requests, with file:line.

# Template: Bug Report

*Guidance: a good bug report lets someone who's never seen this system
reproduce the problem without asking you a single follow-up question.
If you find yourself unsure of a field, that's the field to go verify
before filing — an unverified guess presented as fact wastes the fixer's
time.*

---

# Bug: {One-line summary of the observable problem}

**Severity:** {Critical / High / Medium / Low}
**Reported by:** {name} · **Date:** {date}
**Affects:** {version/environment/component}

## Expected behavior

{What should happen.}

## Actual behavior

{What actually happens. Be precise — "it doesn't work" is not a bug
report.}

## Steps to reproduce

1. {Exact step}
2. {Exact step}
3. {Observe: the actual failure}

**Reproducibility:** {Always / Intermittent (~X% of attempts) / Only under
specific condition: {condition}}

## Environment

- {OS/browser/runtime version}
- {Relevant config or feature flags active}
- {Data/state conditions needed to trigger it, if any}

## Evidence

{Logs, stack trace, screenshot, or recording. Paste the actual raw
output — a paraphrase of an error message is not as useful as the
message itself.}

```
{stack trace / log excerpt}
```

## Impact

{Who/what is affected, and how badly — this drives prioritization more
than the technical details do.}

## What's already been ruled out

{If you've investigated at all — what you checked and eliminated, so the
next person doesn't repeat it.}

## Suspected cause (optional)

{Only if you have a genuine, evidence-based hypothesis — mark clearly as
a hypothesis, not a diagnosis.}

---

## Best practices for this template
- Paste raw logs/errors, not paraphrases — exact text is what lets
  someone grep for the actual failure point.
- State reproducibility honestly; "always" vs. "intermittent" changes
  the entire debugging approach (see
  `../Prompt-Library/Debugging/flaky-test-diagnosis.md`).
- Separate observed fact (actual behavior, evidence) from hypothesis
  (suspected cause) — don't let a guess masquerade as a finding.

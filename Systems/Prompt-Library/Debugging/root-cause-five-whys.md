# Root Cause — Five Whys, Evidence-Driven

*Purpose:* Drive a bug investigation past the symptom to the actual root cause using disciplined causal chaining, refusing to stop at "it's flaky."

## When to use
Any bug where the first fix attempt didn't stick, or where "just retry it" is being proposed as a fix.

## Expected input
Symptom description, logs/stack traces, and what's already been ruled out.

## Expected output
A causal chain from symptom to root cause, each link backed by evidence, plus the fix that addresses the root, not the symptom.

## The complete prompt
```
We are root-causing a bug. Do not propose a fix until the root cause is
identified with evidence at every step.

Symptom: {symptom}
Evidence available: {logs, stack traces, repro steps}
Already ruled out: {what's been checked}

Process:
1. State the symptom precisely — what exactly is observed, under what
   conditions, how reproducible.
2. Ask "why" and answer only using the evidence provided or explicitly
   flag "unverified — would need to check X." Do not guess silently.
3. Repeat until you reach a cause that, if fixed, would make the symptom
   structurally impossible (not just less likely).
4. State the fix that addresses that root cause, and name the layer where
   it belongs (code, config, infra, process).
5. State what a fix at each intermediate layer would fail to prevent.

If the evidence is insufficient to complete the chain, stop and list
exactly what additional evidence (which log, which metric, which test)
would resolve the next "why."
```

## Variants
- **Production incident variant:** add a timeline reconstruction step before the five whys.
- **Intermittent bug variant:** add explicit hypothesis-ranking by likelihood before investigating each.

## Related prompts
- [[production-incident-triage]]
- [[legacy-code-onboarding]]

## Tips
- Feed in raw logs, not summaries — summaries already discard the evidence needed for the chain.
- If the model guesses without evidence, explicitly reject the guess and ask it to name what evidence would confirm/deny it.

## Common mistakes
- Stopping at the first plausible-sounding cause instead of continuing until it's structurally root.
- Accepting "race condition" or "flaky test" as a terminal answer without identifying the actual shared-state or ordering issue.

## Example
Input: symptom = "checkout fails ~2% of the time with a null pointer," evidence = stack trace + recent deploy log.
Expected output: chain from null pointer → unguarded optional field → upstream service occasionally omits the field under load → root cause: no schema validation at the ingestion boundary.

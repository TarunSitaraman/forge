# Flaky Test Diagnosis

*Purpose:* Distinguish a genuinely flaky test from a real intermittent bug the test is correctly catching, and fix the actual cause instead of adding retries.

## When to use
A test fails intermittently in CI and the instinct is to mark it `@flaky` or add a retry.

## Expected input
The test code, the failure(s) (with any diffs/stack traces from failing runs), and whether it fails locally or only in CI.

## Expected output
A classification (test bug vs. real production bug vs. environment issue) with evidence, and a fix — never "just add a retry" as the terminal answer.

## The complete prompt
```
This test is flaky: {test code}
Failure output(s) from failing runs: {failure logs/diffs}
Fails locally: {yes/no}. Fails in CI only: {yes/no}. Failure rate: {rate}.

Classify the root cause into exactly one of:
A. Test bug — shared state, ordering dependency, timing assumption, or
   non-deterministic input in the TEST itself.
B. Real bug — the test is correctly catching a genuine race condition or
   non-determinism in the code under test.
C. Environment issue — CI resource contention, clock skew, external
   dependency flakiness.

Justify the classification with specific evidence from the failure output,
not general knowledge about "tests are often flaky because X."

Then give the fix:
- If A: the specific isolation/ordering fix.
- If B: escalate — this is a real bug, not a test problem; state what in
  the code under test needs fixing.
- If C: the environment fix (e.g. explicit wait conditions, resource limits).

Never propose "add a retry" or "increase timeout" as the final fix unless
you've also identified why the retry/timeout addresses the actual cause.
```

## Variants
- **Async/UI test variant:** focus classification on race conditions between render and assertion.
- **Distributed system test variant:** focus on network partition / clock skew as environment-issue candidates.

## Related prompts
- [[root-cause-five-whys]]
- [[pr-review-senior-engineer]]

## Tips
- Always supply multiple failure instances if available — pattern across failures is more diagnostic than one instance.
- Push back hard if the model's first suggestion is "add a retry" without classification.

## Common mistakes
- Defaulting to retries, which hides real bugs (classification B) and lets them ship to production.
- Diagnosing from a single failure log when the failure is intermittent — ask for multiple instances.

## Example
Input: a test asserting on a background job's output, failing ~5% of CI runs only.
Expected output: classifies as A (test bug) — the test doesn't wait for the job to complete before asserting — and gives the explicit wait-for-completion fix.

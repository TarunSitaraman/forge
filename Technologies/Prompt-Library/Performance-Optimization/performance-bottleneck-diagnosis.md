# Performance Bottleneck Diagnosis

## Purpose
Find the actual bottleneck behind a real performance complaint using
profiling evidence, before proposing any fix — optimizing the wrong part
of a system is a common and expensive mistake.

## When to Use
When something is "slow" and the fix isn't obvious — before writing any
optimization code.

## Expected Inputs
The specific slow operation, profiling data if available (flame graph,
timing breakdown, query plan), and the scale at which it's slow (data
size, request volume).

## Expected Outputs
A ranked list of actual bottlenecks with evidence, not guesses, and an
explicit statement of what NOT to optimize because it's not the
bottleneck.

## Complete Prompt
```
Diagnose this performance problem using the evidence given — don't guess
at generic "common performance issues."

Slow operation: {what's slow, and the observed latency/throughput}
Profiling data (if any): {flame graph summary, timing breakdown, query
plan, or "none yet"}
Scale: {data size, request volume, concurrency}

Process:
1. If no profiling data exists, tell me exactly what to capture first
   (which profiler, what to measure) — do not speculate about the cause
   without evidence when it's available to gather.
2. From the evidence given, identify where time is ACTUALLY spent —
   rank the top contributors by real percentage of total time, not by
   which part of the code looks suspicious.
3. For the top contributor, give the specific mechanism (N+1 query,
   unindexed scan, synchronous I/O blocking a hot path, algorithmic
   complexity mismatch with data size) — not a vague "this could be
   faster."
4. Explicitly state which parts of the system are NOT bottlenecks based
   on the evidence, so effort isn't wasted optimizing them.
5. Recommend the fix for the top bottleneck only — re-profile after
   applying it before optimizing the next one, since fixing one
   bottleneck often changes where the next one is.
```

## Variations
- **Database-specific variant:** require the actual query plan
  (EXPLAIN ANALYZE or equivalent) as evidence before diagnosing.
- **Distributed system variant:** add explicit check for network
  round-trips / serialization overhead as a bottleneck class distinct
  from CPU-bound work.

## Tips
- Always profile before optimizing; intuition about where time is spent is wrong more often than engineers expect.
- Re-profile after each fix — the next bottleneck is rarely where you'd guess.

## Common Mistakes
- Optimizing the part of the code that looks complex or unfamiliar
  instead of the part profiling data actually shows is slow.
- Applying a fix and declaring victory without re-profiling — the
  bottleneck often moves elsewhere after the first fix.
- Micro-optimizing a function that accounts for 2% of total time while
  ignoring one that accounts for 60%.

## Example Usage
Input: an API endpoint that's slow, with a flame graph showing 70% of
time in a single ORM call producing an N+1 query pattern. Expected
output: identifies the N+1 pattern as the dominant bottleneck with the
specific query count evidence, recommends eager loading, explicitly
notes the serialization layer (a smaller % of time) is not worth
optimizing yet.

## Related Prompts
- [[optimization-tradeoff-review]]
- [[root-cause-five-whys]]
- [[scalability-stress-test-review]]

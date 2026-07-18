# Optimization Tradeoff Review

## Purpose
Review a proposed performance optimization for what it actually costs
(complexity, memory, maintainability) before adopting it — a faster
implementation that's unmaintainable or fragile isn't automatically a
win.

## When to Use
After identifying a specific optimization (caching, denormalization, an
algorithmic change, precomputation) and before implementing it.

## Expected Inputs
The proposed optimization, the performance gain it targets, and the
current code/system it would change.

## Expected Outputs
An honest cost/benefit assessment — the real gain, the real cost, and a
recommendation, including "don't do this" as a valid outcome.

## Complete Prompt
```
Review this proposed optimization's tradeoffs honestly — including
whether it's worth doing at all.

Optimization: {description — e.g. add a cache, denormalize a table,
switch algorithm, precompute a value}
Target gain: {expected latency/throughput improvement, and how confident
is this estimate}
Current system: {relevant code/architecture}

Assess:
1. Is the target gain actually needed given real usage (see
   `performance-bottleneck-diagnosis.md` if this hasn't been confirmed
   yet), or is this optimizing something that isn't actually a
   bottleneck at current or realistic future scale?
2. What complexity does this add — new invalidation logic (for a
   cache), new consistency risk (for denormalization), new failure modes
   (for precomputation)? Name them specifically, not generically.
3. What's the maintenance cost — will future engineers need to
   understand this optimization to safely change the surrounding code,
   or is it isolated enough to ignore?
4. Is there a simpler optimization that gets most of the benefit with
   less cost (e.g. an index instead of denormalization, a shorter TTL
   instead of complex invalidation)?
5. Give a clear recommendation: worth it, worth it with a specific
   simplification, or not worth it given the actual bottleneck evidence.
```

## Variations
- **Premature-optimization check variant:** run this BEFORE any
  optimization work starts, specifically to challenge whether the
  bottleneck is real (paired with `performance-bottleneck-diagnosis.md`).
- **Caching-specific variant:** require explicit invalidation strategy
  and staleness-tolerance analysis as part of the cost assessment.

## Tips
- Pair this with `performance-bottleneck-diagnosis.md` first — reviewing tradeoffs for a non-bottleneck wastes the exercise.
- A simpler partial fix (an index) is often worth trying before a complex full fix (denormalization).

## Common Mistakes
- Adopting an optimization based on the target gain alone without
  weighing the real complexity/maintenance cost it introduces.
- Optimizing a path that profiling hasn't confirmed is an actual
  bottleneck, adding complexity for a gain that doesn't matter in
  practice.
- Choosing a complex optimization (denormalization) when a simpler one
  (an index) would achieve most of the benefit.

## Example Usage
Input: proposal to add a Redis cache in front of a database query,
target gain = "reduce p99 latency from 300ms to 20ms." Expected output:
confirms the query is a real bottleneck, but flags that the query lacks
an index that alone might get latency to 60ms with no new
invalidation-complexity cost, recommending trying that first.

## Related Prompts
- [[performance-bottleneck-diagnosis]]
- [[decision-tradeoff-matrix]]
- [[architecture-decision-record]]

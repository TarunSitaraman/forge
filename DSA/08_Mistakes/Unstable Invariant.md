---
type: mistake
status: stable
tags: [dsa/mistake, dsa/unstable-invariant]
canonical: true
related: [[[Binary Search]], [[Sliding Window]], [[Greedy]], [[Monotonic Stack]]]
---
# Unstable Invariant

## Symptoms
The code appears plausible but a key condition stops being true after updates.

## Cause
The invariant was implied rather than stated, or updates mutate state in the wrong order.

## Fix
Write the invariant in one sentence and verify it after each branch of the algorithm.

## Examples
- Binary search returning the neighbor instead of the first valid index.
- Traversal revisiting a state because the state identity was incomplete.
- Window or interval logic using inclusive and half-open boundaries together.

## Detection Checklist
- [ ] Test empty and single-element inputs.
- [ ] Test duplicate values or repeated states.
- [ ] Test first and last valid positions.
- [ ] Verify every loop or recursive branch makes progress.

## Related Patterns
- [[Binary Search]], [[Sliding Window]], [[Greedy]], [[Monotonic Stack]]

## Related Problems
- [[Problem Index]]

## Prevention
During review, ask what invariant should hold before and after the line most likely to mutate state. If the invariant cannot be stated simply, the implementation is not ready.


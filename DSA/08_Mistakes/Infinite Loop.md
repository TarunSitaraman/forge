---
type: mistake
status: stable
tags: [dsa/mistake, dsa/infinite-loop]
canonical: true
related: [[[Two Pointers]], [[Binary Search]], [[BFS]], [[Recursion]]]
---
# Infinite Loop

## Symptoms
The algorithm stops making progress and never reaches a termination condition.

## Cause
Pointers, bounds, queue state, or recursion parameters are not advanced in every branch.

## Fix
Assert progress after each branch and test minimum cases that enter every branch.

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
- [[Two Pointers]], [[Binary Search]], [[BFS]], [[Recursion]]

## Related Problems
- [[Problem Index]]

## Prevention
During review, ask what invariant should hold before and after the line most likely to mutate state. If the invariant cannot be stated simply, the implementation is not ready.


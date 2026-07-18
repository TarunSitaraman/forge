---
type: mistake
status: stable
tags: [dsa/mistake, dsa/recursion-depth]
canonical: true
related: [[[DFS]], [[Recursion]], [[Tree Traversal]], [[Backtracking]]]
---
# Recursion Depth

## Symptoms
Recursive code fails on deep input or creates excessive stack growth.

## Cause
The recursion depth is proportional to input size, tree height, or graph path length.

## Fix
Switch to an explicit stack, rebalance traversal assumptions, or document recursion limits when constraints are small.

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
- [[DFS]], [[Recursion]], [[Tree Traversal]], [[Backtracking]]

## Related Problems
- [[Problem Index]]

## Prevention
During review, ask what invariant should hold before and after the line most likely to mutate state. If the invariant cannot be stated simply, the implementation is not ready.


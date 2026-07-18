---
type: mistake
status: stable
tags: [dsa/mistake, dsa/duplicate-handling]
canonical: true
related: [[[Two Pointers]], [[Backtracking]], [[Binary Search]], [[Sorting]]]
---
# Duplicate Handling

## Symptoms
A solution counts duplicate values or states incorrectly.

## Cause
The algorithm assumes distinct values where equality changes ordering, grouping, or pruning.

## Fix
Decide whether duplicates are separate items or equivalent states, then encode that rule in comparisons and keys.

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
- [[Two Pointers]], [[Backtracking]], [[Binary Search]], [[Sorting]]

## Related Problems
- [[Problem Index]]

## Prevention
During review, ask what invariant should hold before and after the line most likely to mutate state. If the invariant cannot be stated simply, the implementation is not ready.


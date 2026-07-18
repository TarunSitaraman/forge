---
type: mistake
status: stable
tags: [dsa/mistake, dsa/wrong-mid-calculation]
canonical: true
related: [[[Binary Search]], [[Searching Complexity]]]
---
# Wrong Mid Calculation

## Symptoms
Binary search loops fail to terminate or return the adjacent position.

## Cause
Midpoint, update rule, and interval convention do not match.

## Fix
Choose one convention, usually half-open `[lo, hi)`, and make each branch shrink the interval.

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
- [[Binary Search]], [[Searching Complexity]]

## Related Problems
- [[Problem Index]]

## Prevention
During review, ask what invariant should hold before and after the line most likely to mutate state. If the invariant cannot be stated simply, the implementation is not ready.


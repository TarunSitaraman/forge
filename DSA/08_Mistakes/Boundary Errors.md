---
type: mistake
status: stable
tags: [dsa/mistake, dsa/boundary-errors]
canonical: true
related: [[[Matrix Traversal]], [[Prefix Sum]], [[Interval Processing]], [[Tree Traversal]]]
---
# Boundary Errors

## Symptoms
Code fails at empty, first, last, or just-outside positions.

## Cause
Preconditions and valid index ranges are not written down before implementation.

## Fix
Handle empty input early, centralize bounds checks, and test first/last valid indexes.

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
- [[Matrix Traversal]], [[Prefix Sum]], [[Interval Processing]], [[Tree Traversal]]

## Related Problems
- [[Problem Index]]

## Prevention
During review, ask what invariant should hold before and after the line most likely to mutate state. If the invariant cannot be stated simply, the implementation is not ready.


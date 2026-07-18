---
type: mistake
status: stable
tags: [dsa/mistake, dsa/off-by-one]
canonical: true
related: [[[Binary Search]], [[Sliding Window]], [[Prefix Sum]], [[Interval Processing]]]
---
# Off-by-One

## Symptoms
A loop, boundary, or count includes one element too many or too few.

## Cause
Inclusive/exclusive ranges are mixed, zero-based indexes are counted as one-based positions, or window length is computed incorrectly.

## Fix
Write intervals explicitly as `[left, right)` or `[left, right]`, derive length from that choice, and test empty plus single-element cases.

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
- [[Binary Search]], [[Sliding Window]], [[Prefix Sum]], [[Interval Processing]]

## Related Problems
- [[Problem Index]]

## Prevention
During review, ask what invariant should hold before and after the line most likely to mutate state. If the invariant cannot be stated simply, the implementation is not ready.


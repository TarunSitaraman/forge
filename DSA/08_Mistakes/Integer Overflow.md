---
type: mistake
status: stable
tags: [dsa/mistake, dsa/integer-overflow]
canonical: true
related: [[[Binary Search]], [[Bit Manipulation]], [[Hashing]], [[Fast Exponentiation]]]
---
# Integer Overflow

## Symptoms
Arithmetic silently exceeds the representable range in fixed-width languages or external systems.

## Cause
Intermediate products or sums are larger than the final answer type, especially in midpoint, multiplication, hashing, or DP counts.

## Fix
Use wider types, modular arithmetic deliberately, and midpoint formulas such as `lo + (hi - lo) // 2`.

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
- [[Binary Search]], [[Bit Manipulation]], [[Hashing]], [[Fast Exponentiation]]

## Related Problems
- [[Problem Index]]

## Prevention
During review, ask what invariant should hold before and after the line most likely to mutate state. If the invariant cannot be stated simply, the implementation is not ready.


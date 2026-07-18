---
type: mistake
status: stable
tags: [dsa/mistake, dsa/mutable-defaults]
canonical: true
related: [[[Recursion]], [[Backtracking]], [[Dynamic Programming]]]
---
# Mutable Defaults

## Symptoms
State leaks across function calls in Python because a default list, dict, or set is reused.

## Cause
Default arguments are evaluated once when the function is defined.

## Fix
Use `None` defaults and create the mutable object inside the function.

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
- [[Recursion]], [[Backtracking]], [[Dynamic Programming]]

## Related Problems
- [[Problem Index]]

## Prevention
During review, ask what invariant should hold before and after the line most likely to mutate state. If the invariant cannot be stated simply, the implementation is not ready.


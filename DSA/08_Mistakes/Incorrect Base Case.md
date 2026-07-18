---
type: mistake
status: stable
tags: [dsa/mistake, dsa/incorrect-base-case]
canonical: true
related: [[[Dynamic Programming]], [[Recursion]], [[Backtracking]]]
---
# Incorrect Base Case

## Symptoms
Recursive or DP solutions fail on smallest inputs or skip valid solutions.

## Cause
Base cases do not match the recurrence state definition.

## Fix
Define the meaning of each state first, then derive base cases from that meaning.

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
- [[Dynamic Programming]], [[Recursion]], [[Backtracking]]

## Related Problems
- [[Problem Index]]

## Prevention
During review, ask what invariant should hold before and after the line most likely to mutate state. If the invariant cannot be stated simply, the implementation is not ready.


---
type: mistake
status: stable
tags: [dsa/mistake, dsa/stale-heap-entry]
canonical: true
related: [[[Heap]], [[Priority Queue]], [[Dijkstra]], [[Prim]]]
---
# Stale Heap Entry

## Symptoms
A heap returns an item that is no longer valid for the current state.

## Cause
Python heaps do not support efficient arbitrary deletion or priority decrease.

## Fix
Use lazy deletion with a validity check, or push updated entries and ignore stale ones when popped.

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
- [[Heap]], [[Priority Queue]], [[Dijkstra]], [[Prim]]

## Related Problems
- [[Problem Index]]

## Prevention
During review, ask what invariant should hold before and after the line most likely to mutate state. If the invariant cannot be stated simply, the implementation is not ready.


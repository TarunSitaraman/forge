---
type: mistake
status: stable
tags: [dsa/mistake, dsa/missing-visited-set]
canonical: true
related: [[[DFS]], [[BFS]], [[Graph Traversal]], [[Matrix Traversal]]]
---
# Missing Visited Set

## Symptoms
Graph or grid traversal revisits states, loops forever, or overcounts components.

## Cause
The traversal does not record completed or enqueued states at the right time.

## Fix
Mark states when they are enqueued or first entered, and define the state identity clearly.

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
- [[DFS]], [[BFS]], [[Graph Traversal]], [[Matrix Traversal]]

## Related Problems
- [[Problem Index]]

## Prevention
During review, ask what invariant should hold before and after the line most likely to mutate state. If the invariant cannot be stated simply, the implementation is not ready.


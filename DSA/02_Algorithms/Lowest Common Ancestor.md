---
type: algorithm
status: stable
tags: [dsa/algorithm, dsa/lowest-common-ancestor]
canonical: true
related: [[[Binary Lifting]], [[Tree Traversal]]]
---
# Lowest Common Ancestor

## Overview
Tree query for the deepest node that is an ancestor of two nodes.

## Intuition
The algorithm is useful when the problem exposes a structure that can be repeatedly reduced without losing the desired answer. The implementation should make that reduction explicit through a maintained invariant, ordering rule, or recurrence.

## Formal Explanation
Define the input domain, the state maintained by the algorithm, and the operation that advances the state. At every step, the algorithm either finalizes a piece of information, narrows the candidate space, or combines solved subproblems. The final answer is correct only if each reduction preserves all feasible optimal answers or proves that discarded candidates cannot improve the result.

## Correctness
A correctness argument should identify the invariant, prove it holds initially, prove each update preserves it, and show that termination converts the invariant into the desired result. For greedy algorithms, include an exchange argument. For dynamic programs, prove the recurrence covers all valid choices exactly once or intentionally aggregates equivalent states.

## Complexity
O(n log n) preprocessing and O(log n) query with binary lifting.

## Implementation
Python implementations should prefer clear data structures from the standard library, especially list, dict, set, deque, and heapq. Keep reusable code in [[Template Index]] pages and keep problem-specific adaptations in [[Problem Index]].

## Applications
Tree path queries, distance queries, and hierarchy analysis.

## Edge Cases
- Empty or minimal input
- Duplicate values, parallel edges, or equal weights where applicable
- Disconnected structures
- Boundary values that stress indexing or overflow assumptions

## Common Bugs
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Missing Visited Set]]

## Related Patterns
- [[Binary Lifting]], [[Tree Traversal]]
- [[Pattern Index]]

## Related Data Structures
- [[Array]]
- [[Hash Map]]
- [[Heap]]
- [[Graph]]

## Python Templates
- [[Template Index]]

## Interview Notes
- [[Coding Interviews]]
- [[Optimization]]

## Cheat Sheets
- [[Complexities Cheat Sheet]]


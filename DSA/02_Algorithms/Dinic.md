---
type: algorithm
status: stable
tags: [dsa/algorithm, dsa/dinic]
canonical: true
related: [[[BFS]], [[DFS]], [[Graph Traversal]]]
---
# Dinic

## Overview
Maximum flow algorithm using BFS level graphs and DFS blocking flows.

## Intuition
The algorithm is useful when the problem exposes a structure that can be repeatedly reduced without losing the desired answer. The implementation should make that reduction explicit through a maintained invariant, ordering rule, or recurrence.

## Formal Explanation
Define the input domain, the state maintained by the algorithm, and the operation that advances the state. At every step, the algorithm either finalizes a piece of information, narrows the candidate space, or combines solved subproblems. The final answer is correct only if each reduction preserves all feasible optimal answers or proves that discarded candidates cannot improve the result.

## Correctness
A correctness argument should identify the invariant, prove it holds initially, prove each update preserves it, and show that termination converts the invariant into the desired result. For greedy algorithms, include an exchange argument. For dynamic programs, prove the recurrence covers all valid choices exactly once or intentionally aggregates equivalent states.

## Complexity
O(V^2E) general bound, faster on many structured networks.

## Implementation
Python implementations should prefer clear data structures from the standard library, especially list, dict, set, deque, and heapq. Keep reusable code in [[Template Index]] pages and keep problem-specific adaptations in [[Problem Index]].

## Applications
Large flow networks and matching problems requiring stronger performance.

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
- [[BFS]], [[DFS]], [[Graph Traversal]]
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


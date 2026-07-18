---
type: data-structure
status: stable
tags: [dsa/data-structure, dsa/tree]
canonical: true
related: [[[Tree Traversal]], [[DFS]], [[BFS]], [[Binary Lifting]]]
---
# Tree

## Overview
Connected acyclic graph with hierarchical structure and unique simple paths.

## Design
The design should make the supported operations natural and make unsupported operations visibly expensive. A data structure is not just storage; it encodes a contract about access patterns, mutation cost, ordering, and invariants.

## Core Operations


## Complexity
traversal O(n), query costs depend on augmentation. Always analyze the operation actually used by the algorithm, not only the headline average case.

## Implementation Notes
Python code should use built-in structures when they match the contract: list for arrays/stacks, collections.deque for queues/deques, dict and set for hashing, and heapq for heaps. Custom classes are appropriate when the invariant is the main subject, such as [[Disjoint Set]], [[Trie]], [[Fenwick Tree]], or [[Segment Tree]].

## Use Cases
Use for hierarchy, decomposition, expression structure, and path queries.

## Tradeoffs
The right structure is determined by the dominant operation. Prefer simple built-ins until the problem requires stronger asymptotic guarantees, ordered operations, range aggregation, prefix lookup, or component tracking.

## Edge Cases
- Empty structure behavior
- Duplicate keys or values
- Mutation while iterating
- Boundary indexes and sentinel nodes
- Representation-specific memory overhead

## Common Bugs
- [[Boundary Errors]]
- [[Off-by-One]]
- [[Mutable Defaults]]

## Related Patterns
- [[Tree Traversal]], [[DFS]], [[BFS]], [[Binary Lifting]]
- [[Pattern Index]]

## Related Algorithms
- [[Algorithm Index]]

## Python Templates
- [[Template Index]]

## Interview Implications
Explain why this structure fits the required operations. Interviewers often ask how complexity changes if the input becomes dynamic, ordered, streaming, sparse, or memory-constrained.

## Practical Engineering Considerations
Document representation choices at API boundaries. Changing from a list to a heap, set, or tree changes ordering and mutation semantics, not just performance.


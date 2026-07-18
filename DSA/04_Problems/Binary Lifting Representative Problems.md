---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/binary-lifting]
canonical: true
---
# Binary Lifting Representative Problems

Core problem variants that exercise Binary Lifting for efficient ancestor queries and jump-based tree operations.

## Variant 1: Kth Ancestor Queries
**Recognition:** Find the kth ancestor of a node, with multiple queries expected.
**Mechanics:** Precompute up[node][j] = 2^j-th ancestor; decompose k into binary for O(log k) queries.
**Key Challenge:** Understanding the DP relationship up[i][j] = up[up[i][j-1]][j-1].

- [[Binary Lifting - Kth Ancestor]]
- Variant: Kth ancestor with multiple trees
- Variant: Ancestor at specific depth

## Variant 2: Lowest Common Ancestor (LCA) with Binary Lifting
**Recognition:** Multiple LCA queries on a static tree, need O(log n) per query.
**Mechanics:** Bring both nodes to same depth first, then binary search upward together.
**Key Challenge:** Two-phase approach - equalize depth, then simultaneous jumping.

- Variant: LCA via binary lifting
- Variant: Distance between two nodes (using LCA)
- Variant: Path sum between two nodes

## Variant 3: Jump Game Variants
**Recognition:** Precomputed jump tables for graph/array traversal problems.
**Mechanics:** Similar binary lifting technique applied to functional graphs (each node has one outgoing edge).
**Key Challenge:** Recognizing when problem structure matches functional graph pattern.

- Variant: Kth next visited node in functional graph
- Variant: Cycle detection using jump pointers

## Cross-Linking
- **Pattern:** [[Binary Lifting]]
- **Related Patterns:** [[Tree Traversal]], [[DFS]]
- **Interview Guide:** [[Binary Lifting Interview Guide]]

## Key Mechanics
1. **Precompute:** Build up[node][j] table in O(n log n)
2. **Binary Decomposition:** Any k can be represented as sum of powers of 2
3. **Query:** For each set bit in k, jump using precomputed table

## Complexity Pattern
- **Preprocessing:** O(n log n) time and space
- **Per Query:** O(log n) time

## Common Pitfalls
- Not precomputing before queries (defeats the purpose)
- LOG bound insufficient for tree depth
- Forgetting to handle -1 (no such ancestor) propagation


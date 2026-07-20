---
type: problem
status: stable
tags: [dsa/problem, dsa/binary-lifting]
canonical: true
related: [[[Binary Lifting]], [[Tree Traversal]]]
difficulty: hard
---
# Binary Lifting - Lowest Common Ancestor (Multiple Queries)

## Problem Statement
Given a rooted tree with `n` nodes and `q` queries, each asking for the
lowest common ancestor (LCA) of two nodes, answer all queries
efficiently. Unlike the single-query recursive LCA
([[Tree Traversal - Lowest Common Ancestor]]), assume `q` can be large
(e.g., up to 10^5), making an O(n) per-query approach too slow overall.

**Constraints:**
- Static tree (no updates between queries)
- Need O(log n) per query after preprocessing

## Classification
- **Patterns:** [[Binary Lifting]]
- **Category:** Depth-equalization + simultaneous ancestor jumping

## Pattern Selection
This is canonical for Binary Lifting applied beyond simple kth-ancestor
lookup: LCA-with-many-queries needs the same precomputed jump table, plus
a two-phase query algorithm (equalize depth, then binary-search upward
together) built on top of it.

## Why This Approach Works
**Phase 1 — equalize depth:** if `u` is deeper than `v`, lift `u` up by
`depth[u] - depth[v]` using the binary lifting jump table (same `getKthAncestor` building block).

**Phase 2 — binary search upward together:** once both nodes are at the
same depth, try the largest jump `2^j` such that `up[u][j] != up[v][j]`
(they haven't converged yet) — take that jump for both, and shrink `j`.
When no such jump exists, `up[u][0]` (their direct parents) is the LCA.

**Why jump by largest-safe-power-of-two first:** this is the same binary
representation trick as kth-ancestor — trying from the largest bit down
guarantees we land as close to the LCA as possible without overshooting
past it, in O(log n) total steps.

## Python Solution
```python
import math

class LCA:
    def __init__(self, n: int, parent: list[int], depth: list[int]):
        """
        parent[root] should be -1. depth[root] = 0.
        Time: O(n log n) preprocessing
        """
        self.LOG = max(1, math.ceil(math.log2(n))) if n > 1 else 1
        self.depth = depth
        self.up = [[-1] * self.LOG for _ in range(n)]

        for i in range(n):
            self.up[i][0] = parent[i]
        for j in range(1, self.LOG):
            for i in range(n):
                if self.up[i][j-1] != -1:
                    self.up[i][j] = self.up[self.up[i][j-1]][j-1]

    def _lift(self, node: int, k: int) -> int:
        for j in range(self.LOG):
            if node == -1:
                break
            if k & (1 << j):
                node = self.up[node][j]
        return node

    def query(self, u: int, v: int) -> int:
        """Time: O(log n) per query."""
        if self.depth[u] < self.depth[v]:
            u, v = v, u
        u = self._lift(u, self.depth[u] - self.depth[v])

        if u == v:
            return u

        for j in range(self.LOG - 1, -1, -1):
            if self.up[u][j] != -1 and self.up[u][j] != self.up[v][j]:
                u = self.up[u][j]
                v = self.up[v][j]

        return self.up[u][0]
```

## Reasoning
1. **Preprocess once:** build the `up` jump table exactly as in kth-
   ancestor, plus a `depth` array from a single BFS/DFS pass.
2. **Per query, equalize depth:** lift the deeper node up using the same
   binary-decomposition jump.
3. **If equal after lifting, done:** one was an ancestor of the other.
4. **Otherwise, binary-search upward together:** jump both nodes by the
   same power of two whenever doing so keeps them distinct, from largest
   power down to smallest.
5. **Final answer:** the shared parent one step above where they
   converge.

## Complexity Analysis
- **Preprocessing:** O(n log n) time and space
- **Per query:** O(log n) — depth equalization plus the upward binary
  search, both bounded by tree depth in log steps

## Edge Cases & Validation
```python
# Simple chain: 0 -> 1 -> 2 -> 3, LCA(3, 1) should be 1
lca = LCA(4, [-1,0,1,2], [0,1,2,3])
assert lca.query(3, 1) == 1
assert lca.query(2, 3) == 2  # 2 is ancestor of 3

# Branching tree: 0 is root, children 1 and 2; 1's child is 3
lca2 = LCA(4, [-1,0,0,1], [0,1,1,2])
assert lca2.query(2, 3) == 0
assert lca2.query(1, 3) == 1
```

## Common Mistakes
1. **Skipping depth equalization:** jumping both nodes by the same
   amount before they're at equal depth compares unrelated ancestors and
   gives a wrong result.
2. **Jumping when `up[u][j] == up[v][j]`:** this means they've already
   converged (or would overshoot past the true LCA) — the check must be
   "jump only while they're still distinct."
3. **Off-by-one on the final answer:** after the loop, `u` and `v` are
   each one step *below* the LCA — the answer is `up[u][0]`, not `u`
   itself.
4. **Rebuilding the jump table per query:** defeats the entire purpose;
   must precompute once and reuse across all `q` queries.

## Interview Walkthrough
**Interviewer:** "Answer many LCA queries efficiently on a static tree."

**Your Response:**
1. "A single-query recursive LCA is O(n) worst case — too slow if I
   repeat that for many queries."
2. "I'll precompute binary lifting jump tables once, O(n log n)."
3. "Per query: first equalize depth by lifting the deeper node up."
4. "Then binary-search upward together — jump both nodes by the largest
   power of two that keeps them still distinct, shrinking down. Their
   shared parent at convergence is the LCA."
5. "This gives O(log n) per query after the one-time preprocessing."

## Variations
1. **Distance Between Two Nodes:** `depth[u] + depth[v] - 2*depth[lca]`
   once LCA is known
2. **Kth Ancestor:** the simpler building block this LCA algorithm reuses
   directly ([[Binary Lifting - Kth Ancestor]])
3. **Euler Tour + Sparse Table LCA:** an alternative O(1)-per-query
   approach with different preprocessing tradeoffs

## Related Problems
- [[Binary Lifting - Kth Ancestor]] (the jump-table building block this
  problem extends)
- [[Tree Traversal - Lowest Common Ancestor]] (single-query recursive
  version — compare tradeoffs directly)
- [[Binary Lifting Representative Problems]]

## Related Concepts
- [[Binary Lifting]] — core precomputed-jump technique
- [[Tree Traversal]] — depth computation via initial DFS/BFS pass


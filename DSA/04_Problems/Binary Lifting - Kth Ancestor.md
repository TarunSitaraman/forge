---
type: problem
status: stable
tags: [dsa/problem, dsa/binary-lifting]
canonical: true
related: [[[Binary Lifting]], [[Tree]]]
difficulty: hard
---
# Binary Lifting - Kth Ancestor of Tree Node

## Problem Statement
Given a tree with n nodes rooted at node 0, implement a function to find the kth ancestor of a given node. Support multiple queries efficiently.

**Constraints:**
- Multiple getKthAncestor queries expected
- n up to 5×10^4, queries up to 5×10^4

## Classification
- **Patterns:** [[Binary Lifting]]
- **Category:** Precomputed ancestor jumping for efficient repeated queries

## Pattern Selection
This is canonical for Binary Lifting because multiple ancestor queries make O(log n) per query (after O(n log n) preprocessing) far more efficient than O(k) per query with naive parent-following.

## Why Binary Lifting Works
**Key Insight:** Precompute up[node][j] = 2^j-th ancestor of node. Any k can be decomposed into powers of 2 (binary representation), allowing us to jump directly using precomputed table.

## Python Solution
```python
import math

class TreeAncestor:
    def __init__(self, n: int, parent: list[int]):
        """
        Precompute binary lifting table.
        Time: O(n log n) preprocessing
        Space: O(n log n)
        """
        self.LOG = max(1, math.ceil(math.log2(n))) if n > 1 else 1
        self.up = [[-1] * self.LOG for _ in range(n)]
        
        for i in range(n):
            self.up[i][0] = parent[i]
        
        for j in range(1, self.LOG):
            for i in range(n):
                if self.up[i][j-1] != -1:
                    self.up[i][j] = self.up[self.up[i][j-1]][j-1]
    
    def getKthAncestor(self, node: int, k: int) -> int:
        """
        Find kth ancestor using precomputed jumps.
        Time: O(log k) per query
        """
        for j in range(self.LOG):
            if node == -1:
                break
            if k & (1 << j):
                node = self.up[node][j]
        return node
```

## Reasoning
1. **Precompute Table:** up[i][j] = 2^j-th ancestor of node i, built using DP: up[i][j] = up[up[i][j-1]][j-1]
2. **Base Case:** up[i][0] = direct parent
3. **Query Processing:** Decompose k into binary; for each set bit, jump using precomputed table
4. **Efficiency:** O(n log n) preprocessing enables O(log k) per query instead of O(k)

## Complexity Analysis
- **Preprocessing:** O(n log n) time and space
- **Per Query:** O(log k) time

## Edge Cases & Validation
```python
# Simple chain: 0 -> 1 -> 2 -> 3
ta = TreeAncestor(4, [-1, 0, 1, 2])
assert ta.getKthAncestor(3, 1) == 2
assert ta.getKthAncestor(3, 2) == 1
assert ta.getKthAncestor(3, 3) == 0
assert ta.getKthAncestor(3, 4) == -1  # No such ancestor
```

## Common Mistakes
1. **Not Precomputing Before Queries:** Defeats the purpose of binary lifting
2. **Wrong LOG Bound:** Must cover log2(max possible k or tree depth)
3. **Not Handling -1 (No Ancestor) Propagation:** Must break early if node becomes -1

## Interview Walkthrough
**Interviewer:** "Find kth ancestor efficiently, multiple queries expected."

**Your Response:**
1. "Naive approach follows parent pointers k times—O(k) per query, too slow for many queries."
2. "I'll precompute up[node][j] = 2^j-th ancestor using DP: up[i][j] = up[up[i][j-1]][j-1]."
3. "For any query k, decompose into binary and jump using precomputed powers of 2."
4. "This gives O(n log n) preprocessing, O(log k) per query."

## Related Problems
- [[Binary Lifting]] pattern (LCA problems also use this technique)

## Related Concepts
- [[Binary Lifting]] — core technique
- [[Tree]] — data structure context


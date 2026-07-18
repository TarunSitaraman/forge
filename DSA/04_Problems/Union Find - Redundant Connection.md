---
type: problem
status: stable
tags: [dsa/problem, dsa/union-find]
canonical: true
related: [[[Union Find]], [[Disjoint Set]]]
difficulty: medium
---
# Union Find - Redundant Connection

## Problem Statement
Given a graph that started as a tree with n nodes, one additional edge was added, creating exactly one cycle. Find that redundant edge. If multiple answers exist, return the one that appears last in the input.

**Constraints:**
- n nodes, n edges (tree + 1 extra edge)
- Edges given as [u, v] pairs
- Exactly one cycle exists

## Classification
- **Patterns:** [[Union Find]]
- **Category:** Cycle detection in undirected graph via incremental edge addition

## Pattern Selection
This is canonical for Union-Find because:
1. Processing edges incrementally (one at a time)
2. Need to detect when an edge creates a cycle (connects already-connected nodes)
3. Union-Find's `union()` naturally returns False when nodes already connected
4. Perfect fit: first edge that fails to union (already same component) is the answer

## Why Union-Find Works
**Invariant:** If two nodes are already in the same component, adding an edge between them creates a cycle.

**Key Insight:**
- Process edges in given order
- For each edge (u, v): if find(u) == find(v), this edge creates the cycle
- Otherwise, union them and continue

## Python Solution
```python
def findRedundantConnection(edges: list[list[int]]) -> list[int]:
    """
    Find the redundant edge causing a cycle using Union-Find.
    Time: O(n * α(n)) - near O(n) with path compression
    Space: O(n) for parent/rank arrays
    """
    n = len(edges)
    parent = list(range(n + 1))  # 1-indexed nodes
    rank = [0] * (n + 1)
    
    def find(x):
        if parent[x] != x:
            parent[x] = find(parent[x])  # Path compression
        return parent[x]
    
    def union(x, y):
        root_x, root_y = find(x), find(y)
        if root_x == root_y:
            return False  # Already connected: this edge is redundant!
        
        # Union by rank
        if rank[root_x] < rank[root_y]:
            root_x, root_y = root_y, root_x
        parent[root_y] = root_x
        if rank[root_x] == rank[root_y]:
            rank[root_x] += 1
        return True
    
    for u, v in edges:
        if not union(u, v):
            return [u, v]  # This edge creates a cycle
    
    return []  # Shouldn't reach here given problem constraints
```

## Reasoning
1. **Initialize Union-Find:** Each node starts as its own component
2. **Process Edges Sequentially:** For each edge, attempt to union the two endpoints
3. **Detect Redundancy:** If union() returns False (already same component), this edge is redundant
4. **Return Immediately:** Since problem guarantees exactly one such edge, return upon first detection
5. **Path Compression + Union by Rank:** Ensures near-O(1) operations

## Complexity Analysis
- **Time:** O(n · α(n)) — α is inverse Ackermann, practically constant
- **Space:** O(n) — parent and rank arrays

## Edge Cases & Validation
```python
# Simple triangle (cycle among first 3 nodes)
edges = [[1,2], [1,3], [2,3]]
assert findRedundantConnection(edges) == [2, 3]

# Cycle appears later
edges = [[1,2], [2,3], [3,4], [1,4], [1,5]]
assert findRedundantConnection(edges) == [1, 4]

# Larger tree with single redundant edge
edges = [[1,2], [2,3], [3,4], [4,1], [1,5]]
assert findRedundantConnection(edges) == [4, 1]

# Star pattern with one extra connecting edge
edges = [[1,2], [1,3], [1,4], [1,5], [2,3]]
assert findRedundantConnection(edges) == [2, 3]
```

## Common Mistakes
1. **Not Using Path Compression:** Degrades to O(n) per find, O(n²) overall
2. **[[Boundary Errors]]:** 1-indexed vs 0-indexed node numbering confusion
3. **Not Returning Immediately:** Should return as soon as redundant edge found (problem guarantees single answer)
4. **Confusing Union-Find with DFS Cycle Detection:** Union-Find is more efficient for this incremental scenario

## Interview Walkthrough
**Interviewer:** "Find the redundant edge causing a cycle in this near-tree structure."

**Your Response:**
1. "Since we're adding edges incrementally, Union-Find is perfect here."
2. "For each edge, I try to union the two nodes."
3. "If they're already in the same component, this edge creates a cycle—that's our answer."
4. "With path compression and union by rank, this runs in near O(n) time."

**Why This Resonates:**
- Shows recognition of "incremental connectivity" as Union-Find signal
- Explains the elegant connection between union() return value and cycle detection
- Demonstrates efficiency awareness (path compression, union by rank)

## Variations
1. **Redundant Connection II (Directed Graph):** More complex - must handle two cases (cycle vs. two parents)
2. **Number of Connected Components:** Related but different goal (count instead of find specific edge)
3. **Graph Valid Tree:** Similar Union-Find check for tree validity

## Related Problems
- [[Union Find - Number of Provinces]] (different application of same data structure)
- [[Union Find Representative Problems]]

## Related Concepts
- [[Union Find]] — core data structure with path compression optimization
- [[Disjoint Set]] — underlying structure
- [[Cycle Detection]] — undirected graph specific approach


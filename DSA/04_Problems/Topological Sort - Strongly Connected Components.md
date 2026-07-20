---
type: problem
status: stable
tags: [dsa/problem, dsa/topological-sort, dsa/graph-traversal]
canonical: true
related: [[[Topological Sort]], [[Kosaraju]], [[DFS]]]
difficulty: hard
---
# Topological Sort - Strongly Connected Components (Kosaraju's Algorithm)

## Problem Statement
Given a directed graph, find all its strongly connected components
(SCCs) — maximal sets of vertices where every vertex can reach every
other vertex in the same set via directed edges.

**Constraints:**
- Graph may have multiple disconnected SCCs
- Each vertex belongs to exactly one SCC

## Classification
- **Patterns:** [[Topological Sort]], [[DFS]]
- **Algorithm:** [[Kosaraju]]
- **Category:** Two-pass DFS using finish-order and graph reversal

## Pattern Selection
This is canonical for the "topological-order-like DFS combined with graph
reversal" technique: a single DFS pass can't distinguish an SCC from a
simple reachability chain, but processing vertices in **decreasing finish
time** on the **reversed graph** provably isolates each SCC.

## Why Kosaraju's Algorithm Works

**The two-pass idea:**
1. **Pass 1:** DFS the original graph, recording each vertex's finish
   time (post-order) — this is exactly the same post-order used to build
   a topological sort.
2. **Pass 2:** DFS the **reversed** graph (all edges flipped), visiting
   vertices in **decreasing** finish-time order from pass 1. Each DFS
   tree produced in this pass is exactly one SCC.

**Intuition for why reversing works:** if vertex `u` finished after `v`
in pass 1, `u` is "outside" or "before" `v`'s component in the DAG of
SCCs (condensation graph). Reversing the graph flips reachability, so
starting from the vertex that finished last in pass 1 and exploring the
reversed graph can only reach vertices within its own SCC — it can't
"leak" into a component that was topologically before it, because that
edge direction no longer exists.

## Python Solution
```python
def kosaraju_scc(n: int, edges: list[tuple[int, int]]) -> list[list[int]]:
    """
    Find all strongly connected components using Kosaraju's algorithm.
    Time: O(V+E) - two DFS passes
    Space: O(V+E) - two graph representations + recursion stack
    """
    graph = [[] for _ in range(n)]
    reverse_graph = [[] for _ in range(n)]
    for u, v in edges:
        graph[u].append(v)
        reverse_graph[v].append(u)

    visited = [False] * n
    finish_order = []

    def dfs1(node):
        visited[node] = True
        for neighbor in graph[node]:
            if not visited[neighbor]:
                dfs1(neighbor)
        finish_order.append(node)  # post-order = finish time

    for i in range(n):
        if not visited[i]:
            dfs1(i)

    visited = [False] * n
    sccs = []

    def dfs2(node, component):
        visited[node] = True
        component.append(node)
        for neighbor in reverse_graph[node]:
            if not visited[neighbor]:
                dfs2(neighbor, component)

    # Process in decreasing finish-time order
    for node in reversed(finish_order):
        if not visited[node]:
            component = []
            dfs2(node, component)
            sccs.append(component)

    return sccs
```

## Reasoning
1. **Pass 1 (original graph):** standard DFS, but the only thing that
   matters is the **order vertices finish** (post-order), not the tree
   structure itself.
2. **Reverse the graph:** flip every edge.
3. **Pass 2 (reversed graph):** process vertices in decreasing finish
   order from pass 1. Each fresh DFS call from an unvisited vertex
   explores exactly one full SCC and nothing more.
4. **Collect:** each pass-2 DFS tree is appended as one SCC.

## Complexity Analysis
- **Time:** O(V+E) — two linear DFS passes plus O(E) to build the
  reversed graph
- **Space:** O(V+E) — original graph, reversed graph, recursion stack

## Edge Cases & Validation
```python
# Two separate cycles, no cross-connectivity
sccs = kosaraju_scc(4, [(0,1),(1,0),(2,3),(3,2)])
assert sorted(sorted(c) for c in sccs) == [[0,1],[2,3]]

# Single SCC (fully cyclic)
sccs = kosaraju_scc(3, [(0,1),(1,2),(2,0)])
assert len(sccs) == 1 and sorted(sccs[0]) == [0,1,2]

# No edges: every vertex is its own SCC
sccs = kosaraju_scc(3, [])
assert len(sccs) == 3

# DAG (no cycles at all): every vertex is its own SCC
sccs = kosaraju_scc(3, [(0,1),(1,2)])
assert len(sccs) == 3
```

## Common Mistakes
1. **Processing pass 2 in increasing (not decreasing) finish order:**
   breaks the algorithm's correctness — the whole point is starting from
   the vertex that finished *last*.
2. **Forgetting to build the reversed graph:** running pass 2 on the
   original graph instead just repeats pass 1's reachability, doesn't
   isolate SCCs.
3. **Confusing SCC with simple connected components:** SCCs require
   *mutual* reachability in a *directed* graph — a component that's only
   reachable one-way is not strongly connected.

## Interview Walkthrough
**Interviewer:** "Find all strongly connected components in this directed
graph."

**Your Response:**
1. "I'll use Kosaraju's algorithm — two DFS passes."
2. "First pass: DFS the graph as-is, recording each vertex's finish
   time — this is the same post-order used for topological sort."
3. "Then I reverse every edge and DFS again, but this time starting from
   vertices in decreasing finish-time order."
4. "Each DFS tree in that second pass is exactly one SCC — reversing the
   graph prevents the search from leaking into components that were
   topologically 'before' the current one."

## Variations
1. **Tarjan's Algorithm:** single-pass alternative using a low-link
   value and an explicit stack, avoids building the reversed graph but
   is more intricate to implement correctly
2. **Condensation Graph:** compress each SCC into a single node — the
   result is guaranteed to be a DAG, useful for further processing
3. **Number of SCCs:** just return `len(sccs)` from this same algorithm

## Related Problems
- [[Topological Sort - Course Schedule]] (post-order/finish-time
  technique this algorithm's first pass reuses)
- [[Topological Sort Representative Problems]]

## Related Concepts
- [[Topological Sort]] — pass 1's finish-order is exactly a topological
  sort of the DAG-like parts of the graph
- [[Kosaraju]] — the named algorithm this problem implements
- [[DFS]] — traversal mechanism for both passes


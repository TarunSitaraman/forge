---
type: python-template
status: stable
tags: [dsa/template, python, dsa/topological-sort]
canonical: true
related: [[Topological Sort]]
---
# Python - Topological Sort

## Purpose
Order DAG vertices after all prerequisites.

## Explanation
This template captures the reusable implementation shape for [[Topological Sort]]. Keep problem-specific naming and validation in the problem page, but preserve the invariant and boundary discipline shown here.

## Complexity
O(V+E) time and O(V) space.

## Code
``python
from collections import deque

def topological_sort(n, edges):
    graph = [[] for _ in range(n)]
    indegree = [0] * n
    for a, b in edges:
        graph[a].append(b)
        indegree[b] += 1
    q = deque(i for i, deg in enumerate(indegree) if deg == 0)
    order = []
    while q:
        node = q.popleft()
        order.append(node)
        for nxt in graph[node]:
            indegree[nxt] -= 1
            if indegree[nxt] == 0:
                q.append(nxt)
    return order if len(order) == n else None
``

## Edge Cases
- Empty input
- Single-element input
- Duplicate values or repeated states
- Inclusive versus exclusive boundary semantics

## Modifications
Use DFS postorder when recursion is clearer; return None or raise when cycles invalidate a DAG assumption.

## Common Interview Variations
- Return the count instead of the object
- Return all valid objects instead of the best one
- Add online updates or streaming input
- Tighten memory from O(n) to O(1) when dependencies allow it

## Common Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Infinite Loop]]


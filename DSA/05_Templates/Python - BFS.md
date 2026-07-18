---
type: python-template
status: stable
tags: [dsa/template, python]
canonical: true
---
# Python - BFS

## Purpose
Reusable interview-ready Python template for [[BFS]].

## Complexity
- Time: Depends on operation and input size; document per problem.
- Space: Depends on auxiliary structures; document per problem.

## Code
``python
from collections import deque

def bfs(start, neighbors):
    q = deque([start])
    seen = {start}
    order = []

    while q:
        node = q.popleft()
        order.append(node)
        for nxt in neighbors(node):
            if nxt in seen:
                continue
            seen.add(nxt)
            q.append(nxt)
    return order
``

## Edge Cases
- Empty input
- Single-element input
- Duplicate values or repeated states
- Disconnected components when graph-shaped

## Common Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Missing Visited Set]]


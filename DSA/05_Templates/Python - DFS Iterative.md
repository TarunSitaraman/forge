---
type: python-template
status: stable
tags: [dsa/template, python]
canonical: true
---
# Python - DFS Iterative

## Purpose
Reusable interview-ready Python template for [[DFS Iterative]].

## Complexity
- Time: Depends on operation and input size; document per problem.
- Space: Depends on auxiliary structures; document per problem.

## Code
``python
def dfs_iterative(start, neighbors):
    stack = [start]
    seen = set()
    order = []
    while stack:
        node = stack.pop()
        if node in seen:
            continue
        seen.add(node)
        order.append(node)
        for nxt in reversed(list(neighbors(node))):
            if nxt not in seen:
                stack.append(nxt)
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


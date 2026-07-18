---
type: python-template
status: stable
tags: [dsa/template, python]
canonical: true
---
# Python - DFS Recursive

## Purpose
Reusable interview-ready Python template for [[DFS Recursive]].

## Complexity
- Time: Depends on operation and input size; document per problem.
- Space: Depends on auxiliary structures; document per problem.

## Code
``python
def dfs(node, neighbors, seen=None, order=None):
    if seen is None:
        seen = set()
    if order is None:
        order = []
    seen.add(node)
    order.append(node)
    for nxt in neighbors(node):
        if nxt not in seen:
            dfs(nxt, neighbors, seen, order)
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


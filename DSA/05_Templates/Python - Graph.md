---
type: python-template
status: stable
tags: [dsa/template, python]
canonical: true
---
# Python - Graph

## Purpose
Reusable interview-ready Python template for [[Graph]].

## Complexity
- Time: Depends on operation and input size; document per problem.
- Space: Depends on auxiliary structures; document per problem.

## Code
``python
from collections import defaultdict

def build_graph(edges, directed=False):
    graph = defaultdict(list)
    for a, b in edges:
        graph[a].append(b)
        if not directed:
            graph[b].append(a)
    return graph
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


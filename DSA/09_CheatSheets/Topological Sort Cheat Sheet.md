---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/topological-sort]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Topological Sort Cheat Sheet

## Signals
Course prerequisites; build order; DAG dependency ordering.

## Core Moves
- Kahn's (BFS): track in-degree, process nodes with in-degree 0
- DFS post-order: reverse the post-order traversal
- Cycle exists if result length != n

## Complexity
O(V+E) both approaches.

## Template Links
- Custom implementation (see [[Topological Sort Interview Guide]])

## Watch For
- Not detecting cycles (check result length)
- Confusing in-degree with out-degree

## Canonical Depth
See [[Topological Sort]] for full explanation.

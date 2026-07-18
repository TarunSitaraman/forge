---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/graph-traversal]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Graph Traversal Cheat Sheet

## Signals
Connected components; reachability; cycle detection; topological ordering.

## Core Moves
- DFS: any path, cycle detection (3-color for directed)
- BFS: shortest path (unweighted)
- Union-Find: connectivity, MST, undirected cycles

## Complexity
O(V+E) for DFS/BFS.

## Template Links
- [[Python - DFS Recursive]], [[Python - BFS]]

## Watch For
- [[Missing Visited Set]]
- Disconnected components missed

## Canonical Depth
See [[Graph Traversal]] for full explanation.

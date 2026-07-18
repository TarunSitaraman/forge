---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/matrix-traversal]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Matrix Traversal Cheat Sheet

## Signals
2D grid problems; spiral/diagonal traversal; island counting; grid pathfinding.

## Core Moves
- 4-directional: [(0,1),(1,0),(0,-1),(-1,0)]
- 8-directional: add diagonals
- Always boundary check before array access
- Mark visited to avoid infinite loops in DFS/BFS

## Complexity
O(rows*cols) typical for full traversal.

## Template Links
- [[Python - DFS Recursive]], [[Python - BFS]]

## Watch For
- [[Boundary Errors]]
- [[Missing Visited Set]]

## Canonical Depth
See [[Matrix Traversal]] for full explanation.

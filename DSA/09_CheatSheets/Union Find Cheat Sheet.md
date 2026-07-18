---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/union-find]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Union Find Cheat Sheet

## Signals
Connectivity queries; MST (Kruskal); cycle detection (undirected); dynamic grouping.

## Core Moves
- find(x): path compression - point directly to root
- union(x,y): union by rank/size - attach smaller tree to larger
- Union returns False if already connected (cycle detected)

## Complexity
O(α(n)) amortized per operation (near O(1)).

## Template Links
- Custom implementation typical (see [[Union Find Interview Guide]])

## Watch For
- Missing path compression (degrades to O(n))
- Missing union by rank (chains form)

## Canonical Depth
See [[Union Find]] for full explanation.

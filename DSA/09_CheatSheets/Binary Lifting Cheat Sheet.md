---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/binary-lifting]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Binary Lifting Cheat Sheet

## Signals
Tree ancestor queries; LCA with multiple queries; kth-ancestor problems.

## Core Moves
- Precompute up[node][j] = 2^j-th ancestor
- up[node][j] = up[up[node][j-1]][j-1]
- Query: decompose k into binary, jump using precomputed table

## Complexity
O(n log n) preprocessing, O(log n) per query.

## Template Links
- [[Python - Binary Lifting]]

## Watch For
- Not precomputing before queries
- LOG bound insufficient for tree depth

## Canonical Depth
See [[Binary Lifting]] for full explanation.

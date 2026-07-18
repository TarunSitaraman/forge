---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/interval-processing]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Interval Processing Cheat Sheet

## Signals
Merge intervals; meeting rooms; overlapping ranges; scheduling.

## Core Moves
- Sort by start (merging) or end (scheduling/greedy)
- Overlap check: current.start <= previous.end
- Merge: update end to max(current.end, previous.end)

## Complexity
O(n log n) - sort dominates.

## Template Links
- Custom implementation typical

## Watch For
- Wrong sort key for the operation
- Off-by-one in overlap boundary (<=vs<)

## Canonical Depth
See [[Interval Processing]] for full explanation.

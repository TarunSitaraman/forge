---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/sweep-line]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Sweep Line Cheat Sheet

## Signals
Events with start/end times; skyline problems; interval coverage.

## Core Moves
- Convert intervals to events: (start, +1), (end, -1)
- Sort events by time
- Sweep tracking cumulative count/state

## Complexity
O(n log n) - sort dominates.

## Template Links
- Custom implementation typical

## Watch For
- Wrong event ordering at same timestamp
- Confusing +1/-1 semantics

## Canonical Depth
See [[Sweep Line]] for full explanation.

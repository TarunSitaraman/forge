---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/monotonic-stack]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Monotonic Stack Cheat Sheet

## Signals
Next greater/smaller element; daily temperatures; histogram; span problems.

## Core Moves
- Store indices, not values
- Next greater: decreasing stack, pop when current > top
- Next smaller: increasing stack, pop when current < top

## Complexity
O(n) - each element pushed/popped once.

## Template Links
- Custom implementation typical

## Watch For
- Wrong monotonic direction
- Storing values instead of indices

## Canonical Depth
See [[Monotonic Stack]] for full explanation.

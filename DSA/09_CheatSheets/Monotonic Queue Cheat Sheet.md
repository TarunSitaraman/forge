---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/monotonic-queue]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Monotonic Queue Cheat Sheet

## Signals
Sliding window maximum/minimum; O(1) amortized window extremum access.

## Core Moves
- Deque storing indices in decreasing (max) or increasing (min) value order
- Remove from back if breaks monotonic property
- Remove from front if outside window

## Complexity
O(n) amortized - each element added/removed once.

## Template Links
- Custom deque-based implementation

## Watch For
- Storing values instead of indices
- Not removing out-of-window elements

## Canonical Depth
See [[Monotonic Queue]] for full explanation.

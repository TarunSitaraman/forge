---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/two-pointers]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Two Pointers Cheat Sheet

## Signals
Sorted array; pair-finding; in-place modification; opposite-end convergence.

## Core Moves
- Left/right pointers from opposite ends OR write/read pointers same direction.
- Move based on comparison (too small/too large) or write-position tracking.
- Never move both pointers arbitrarily—each move must have clear justification.

## Complexity
O(n) time, O(1) space typical.

## Template Links
- [[Python - Two Pointers]]

## Watch For
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Infinite Loop]]

## Canonical Depth
See [[Two Pointers]] for full explanation.

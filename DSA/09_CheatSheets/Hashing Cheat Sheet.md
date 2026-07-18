---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/hashing]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Hashing Cheat Sheet

## Signals
O(1) lookup needed; duplicate detection; frequency counting; two-sum patterns.

## Core Moves
- Hash map: complement lookup for two-sum style
- Hash set: duplicate/existence checking
- Counter: frequency-based problems

## Complexity
O(1) average per operation, O(n) worst case (collisions).

## Template Links
- [[Python Collections Cheat Sheet]]

## Watch For
- Mutable objects as keys (must be hashable)
- Check-then-insert order (self-matching bugs)

## Canonical Depth
See [[Hashing]] for full explanation.

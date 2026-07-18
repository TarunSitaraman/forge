---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/fast-slow-pointers]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Fast & Slow Pointers Cheat Sheet

## Signals
Linked list cycle detection; find middle; palindrome check.

## Core Moves
- Slow +1, Fast +2 each iteration
- If cycle exists, they must meet
- Fast reaching end (null) means no cycle

## Complexity
O(n) time, O(1) space.

## Template Links
- Custom implementation typical

## Watch For
- Null check before fast.next.next
- Off-by-one for even-length lists

## Canonical Depth
See [[Fast & Slow Pointers]] for full explanation.

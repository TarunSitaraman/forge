---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/meet-in-middle]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Meet in the Middle Cheat Sheet

## Signals
n moderate (20-40); brute force O(2^n) too slow; problem splits into independent halves.

## Core Moves
- Split into two halves of size n/2
- Generate all subset sums/combinations for each half: O(2^(n/2))
- Combine via hash map or sorted binary search

## Complexity
O(2^(n/2)) instead of O(2^n).

## Template Links
- Custom implementation typical

## Watch For
- Unbalanced split
- Inefficient combine step

## Canonical Depth
See [[Meet in the Middle]] for full explanation.

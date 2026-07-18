---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/divide-and-conquer]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Divide and Conquer Cheat Sheet

## Signals
Problem splits into independent subproblems; merge combines results.

## Core Moves
1. Divide: split into smaller (usually half) subproblems
2. Conquer: solve recursively
3. Combine: merge results efficiently

## Complexity
Master theorem: T(n) = aT(n/b) + O(n^d)

## Template Links
- [[Merge Sort]], [[Quick Sort]], [[Binary Search]]

## Watch For
- Expensive combine step (defeats purpose)
- Unbalanced division

## Canonical Depth
See [[Divide and Conquer]] for full explanation.

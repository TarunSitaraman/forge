---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/binary-search]
canonical: true
---
# Binary Search Cheat Sheet

## Signals
- Sorted input
- Monotonic yes/no predicate
- Need first, last, minimum feasible, or maximum feasible

## Core Moves
- Use half-open range `[lo, hi)` for lower bound
- Compute `mid = lo + (hi - lo) // 2`
- Move the boundary that is proven impossible
- Return the boundary, then validate if needed

## Complexity
- Time: O(log n)
- Space: O(1)

## Syntax / Template
- [[Python - Binary Search]]

## Watch For
- [[Wrong Mid Calculation]]
- [[Off-by-One]]
- [[Boundary Errors]]


---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/dynamic-programming]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Dynamic Programming Cheat Sheet

## Signals
Optimal substructure; overlapping subproblems; "count ways" or "min/max" with repeated recursive calls.

## Core Moves
1. Define dp[i] meaning precisely
2. Base case(s)
3. Transition formula
4. Fill order (usually increasing i)
5. Answer location

## Complexity
Varies: O(n) 1D, O(n*m) 2D, O(2^n * n) bitmask.

## Template Links
- [[DP Cheat Sheet]] (existing detailed version)

## Watch For
- [[Incorrect Base Case]]
- [[Off-by-One]]
- [[Mutable Defaults]]

## Canonical Depth
See [[Dynamic Programming]] for full explanation.

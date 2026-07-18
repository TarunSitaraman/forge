---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/recursion]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Recursion Cheat Sheet

## Signals
Problem defined in terms of smaller version of itself; tree/graph structure.

## Core Moves
- Define base case (smallest/simplest input)
- Express recursive case using smaller subproblems
- Ensure progress toward base case each call

## Complexity
Varies by problem; watch for exponential blowup without memoization.

## Template Links
- Foundation for [[DFS]], [[Backtracking]], [[Divide and Conquer]]

## Watch For
- [[Incorrect Base Case]]
- [[Recursion Depth]]
- No progress toward termination

## Canonical Depth
See [[Recursion]] for full explanation.

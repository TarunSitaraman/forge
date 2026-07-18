---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/backtracking]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Backtracking Cheat Sheet

## Signals
Generate all permutations/combinations/subsets; constraint satisfaction (N-Queens, Sudoku).

## Core Moves
1. Choose: add to current state
2. Explore: recurse
3. Un-choose: remove from state (backtrack)
- Copy state when saving to results (not reference)

## Complexity
Often O(n!) or O(2^n) depending on branching factor.

## Template Links
- [[Python - Backtracking]]

## Watch For
- [[Mutable Defaults]]
- Forgetting to backtrack/undo

## Canonical Depth
See [[Backtracking]] for full explanation.

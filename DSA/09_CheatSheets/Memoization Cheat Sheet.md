---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/memoization]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Memoization Cheat Sheet

## Signals
Naive recursion recomputes same subproblems; exponential time reducible via caching.

## Core Moves
- Cache dict/array keyed by input parameters
- Check cache before recomputing
- Avoid mutable default argument pitfall (use None, init inside)

## Complexity
Transforms exponential to polynomial (e.g., O(2^n) to O(n)).

## Template Links
- Foundation for top-down DP

## Watch For
- [[Mutable Defaults]]
- Cache key missing state parameters

## Canonical Depth
See [[Memoization]] for full explanation.

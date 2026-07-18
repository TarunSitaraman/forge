---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/bisect]
canonical: true
related: [[Pattern Index]], [[Template Index]], [[Complexities Cheat Sheet]]
---
# Bisect Cheat Sheet

## Signals
Sorted list binary search; bisect_left for first >= x; bisect_right for first > x.

## Core Moves
- Name the invariant or state.
- Choose the simplest structure that supports the required operations.
- Keep boundaries explicit.
- Validate with empty, single, duplicate, and extreme inputs.

## Complexity
- State the dominant operation.
- Include auxiliary space.
- Use amortized language when operations are paid for across a sequence.

## Template Links
- [[Template Index]]

## Watch For
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Missing Visited Set]]
- [[Infinite Loop]]

## Canonical Depth
Use [[Pattern Index]], [[Algorithm Index]], and [[Data Structure Index]] for full explanations.


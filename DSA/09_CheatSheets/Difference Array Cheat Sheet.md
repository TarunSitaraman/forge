---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/difference-array]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Difference Array Cheat Sheet

## Signals
Multiple range updates then single final query; range increment operations.

## Core Moves
- diff[start] += val, diff[end+1] -= val (O(1) per update)
- Final prefix sum of diff array gives actual values
- Extend diff array by 1 for boundary marker

## Complexity
O(n+m) for n elements, m updates.

## Template Links
- [[Python - Difference Array]]

## Watch For
- Off-by-one at range boundary (end vs end+1)
- Forgetting final prefix sum computation

## Canonical Depth
See [[Difference Array]] for full explanation.

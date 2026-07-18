---
type: python-template
status: stable
tags: [dsa/template, python, dsa/difference-array]
canonical: true
related: [[Difference Array]]
---
# Python - Difference Array

## Purpose
Apply many range updates by storing boundary deltas.

## Explanation
This template captures the reusable implementation shape for [[Difference Array]]. Keep problem-specific naming and validation in the problem page, but preserve the invariant and boundary discipline shown here.

## Complexity
O(1) per update and O(n) reconstruction.

## Code
``python
def apply_range_additions(n, updates):
    diff = [0] * (n + 1)
    for left, right, delta in updates:
        diff[left] += delta
        if right + 1 < len(diff):
            diff[right + 1] -= delta
    result = []
    running = 0
    for i in range(n):
        running += diff[i]
        result.append(running)
    return result
``

## Edge Cases
- Empty input
- Single-element input
- Duplicate values or repeated states
- Inclusive versus exclusive boundary semantics

## Modifications
For half-open ranges, subtract at right rather than right + 1.

## Common Interview Variations
- Return the count instead of the object
- Return all valid objects instead of the best one
- Add online updates or streaming input
- Tighten memory from O(n) to O(1) when dependencies allow it

## Common Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Infinite Loop]]


---
type: python-template
status: stable
tags: [dsa/template, python, dsa/bit-manipulation]
canonical: true
related: [[Bit Manipulation]]
---
# Python - Bit Manipulation

## Purpose
Use integer bits as compact boolean state.

## Explanation
This template captures the reusable implementation shape for [[Bit Manipulation]]. Keep problem-specific naming and validation in the problem page, but preserve the invariant and boundary discipline shown here.

## Complexity
O(1) for fixed-width operations; O(2^n) for subset enumeration.

## Code
``python
def enumerate_submasks(mask):
    sub = mask
    result = []
    while sub:
        result.append(sub)
        sub = (sub - 1) & mask
    result.append(0)
    return result
``

## Edge Cases
- Empty input
- Single-element input
- Duplicate values or repeated states
- Inclusive versus exclusive boundary semantics

## Modifications
Use masks for subsets, parity, visited-state compression, and lowbit operations.

## Common Interview Variations
- Return the count instead of the object
- Return all valid objects instead of the best one
- Add online updates or streaming input
- Tighten memory from O(n) to O(1) when dependencies allow it

## Common Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Infinite Loop]]


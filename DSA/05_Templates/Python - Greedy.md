---
type: python-template
status: stable
tags: [dsa/template, python, dsa/greedy]
canonical: true
related: [[Greedy]]
---
# Python - Greedy

## Purpose
Make the locally safe choice after proving it cannot hurt the optimum.

## Explanation
This template captures the reusable implementation shape for [[Greedy]]. Keep problem-specific naming and validation in the problem page, but preserve the invariant and boundary discipline shown here.

## Complexity
Often O(n log n) from sorting or heap operations.

## Code
``python
def erase_overlap_intervals(intervals):
    intervals.sort(key=lambda item: item[1])
    removed = 0
    end = float("-inf")
    for start, finish in intervals:
        if start < end:
            removed += 1
        else:
            end = finish
    return removed
``

## Edge Cases
- Empty input
- Single-element input
- Duplicate values or repeated states
- Inclusive versus exclusive boundary semantics

## Modifications
Change the sort key to match the exchange argument; use a heap when the active best choice changes online.

## Common Interview Variations
- Return the count instead of the object
- Return all valid objects instead of the best one
- Add online updates or streaming input
- Tighten memory from O(n) to O(1) when dependencies allow it

## Common Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Infinite Loop]]


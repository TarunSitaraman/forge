---
type: python-template
status: stable
tags: [dsa/template, python, dsa/interval-processing]
canonical: true
related: [[Interval Processing]]
---
# Python - Interval Processing

## Purpose
Sort intervals and reason about overlap, coverage, or active ranges.

## Explanation
This template captures the reusable implementation shape for [[Interval Processing]]. Keep problem-specific naming and validation in the problem page, but preserve the invariant and boundary discipline shown here.

## Complexity
O(n log n) from sorting, O(n) scan.

## Code
``python
def merge_intervals(intervals):
    intervals.sort()
    merged = []
    for start, end in intervals:
        if not merged or start > merged[-1][1]:
            merged.append([start, end])
        else:
            merged[-1][1] = max(merged[-1][1], end)
    return merged
``

## Edge Cases
- Empty input
- Single-element input
- Duplicate values or repeated states
- Inclusive versus exclusive boundary semantics

## Modifications
Switch comparison for closed versus half-open intervals; use heap for meeting-room allocation.

## Common Interview Variations
- Return the count instead of the object
- Return all valid objects instead of the best one
- Add online updates or streaming input
- Tighten memory from O(n) to O(1) when dependencies allow it

## Common Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Infinite Loop]]


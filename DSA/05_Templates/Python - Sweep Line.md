---
type: python-template
status: stable
tags: [dsa/template, python, dsa/sweep-line]
canonical: true
related: [[Sweep Line]]
---
# Python - Sweep Line

## Purpose
Sort events and update active state as the coordinate advances.

## Explanation
This template captures the reusable implementation shape for [[Sweep Line]]. Keep problem-specific naming and validation in the problem page, but preserve the invariant and boundary discipline shown here.

## Complexity
O(n log n) sorting plus active-state update cost.

## Code
``python
def max_overlap(intervals):
    events = []
    for start, end in intervals:
        events.append((start, 1))
        events.append((end, -1))
    events.sort(key=lambda item: (item[0], item[1]))
    active = best = 0
    for _, delta in events:
        active += delta
        best = max(best, active)
    return best
``

## Edge Cases
- Empty input
- Single-element input
- Duplicate values or repeated states
- Inclusive versus exclusive boundary semantics

## Modifications
Choose endpoint ordering carefully for closed versus half-open intervals.

## Common Interview Variations
- Return the count instead of the object
- Return all valid objects instead of the best one
- Add online updates or streaming input
- Tighten memory from O(n) to O(1) when dependencies allow it

## Common Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Infinite Loop]]


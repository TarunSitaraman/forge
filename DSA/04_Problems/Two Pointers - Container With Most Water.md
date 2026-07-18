---
type: problem
status: stable
tags: [dsa/problem, dsa/two-pointers-container-with-most-water]
canonical: true
related: [[[Two Pointers]], [[Greedy]], [[Python - Two Pointers]]]
---
# Two Pointers - Container With Most Water

## Problem Statement
Maximize area between two vertical lines.

## Classification
- Patterns: [[Two Pointers]], [[Greedy]]
- Template: [[Python - Two Pointers]]

## Pattern Selection
Move the shorter side because the taller side cannot improve area while width shrinks. This page applies canonical theory rather than duplicating it.

## Python Solution
``python
def max_area(height):
    left, right = 0, len(height) - 1
    best = 0
    while left < right:
        best = max(best, (right - left) * min(height[left], height[right]))
        if height[left] < height[right]:
            left += 1
        else:
            right -= 1
    return best
``

## Reasoning
The solution preserves the invariant owned by the linked pattern page, advances monotonically through the input or state space, and records only the state required to produce the answer.

## Complexity
O(n) time and O(1) space.

## Lessons
- State the invariant before coding.
- Keep boundary handling explicit.
- Link reusable reasoning back to canonical pages.

## Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Missing Visited Set]]

## Related Problems
- [[Problem Index]]


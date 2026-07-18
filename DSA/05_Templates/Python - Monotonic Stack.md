---
type: python-template
status: stable
tags: [dsa/template, python, dsa/monotonic-stack]
canonical: true
related: [[Monotonic Stack]]
---
# Python - Monotonic Stack

## Purpose
Maintain an ordered stack of unresolved candidates.

## Explanation
This template captures the reusable implementation shape for [[Monotonic Stack]]. Keep problem-specific naming and validation in the problem page, but preserve the invariant and boundary discipline shown here.

## Complexity
O(n) amortized time, O(n) space.

## Code
``python
def next_greater(nums):
    answer = [-1] * len(nums)
    stack = []
    for i, value in enumerate(nums):
        while stack and nums[stack[-1]] < value:
            answer[stack.pop()] = value
        stack.append(i)
    return answer
``

## Edge Cases
- Empty input
- Single-element input
- Duplicate values or repeated states
- Inclusive versus exclusive boundary semantics

## Modifications
Reverse scan for next-to-left variants; change comparison for greater/smaller and strict/non-strict semantics.

## Common Interview Variations
- Return the count instead of the object
- Return all valid objects instead of the best one
- Add online updates or streaming input
- Tighten memory from O(n) to O(1) when dependencies allow it

## Common Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Infinite Loop]]


---
type: python-template
status: stable
tags: [dsa/template, python, dsa/monotonic-queue]
canonical: true
related: [[Monotonic Queue]]
---
# Python - Monotonic Queue

## Purpose
Maintain a deque of candidate extrema for a moving window.

## Explanation
This template captures the reusable implementation shape for [[Monotonic Queue]]. Keep problem-specific naming and validation in the problem page, but preserve the invariant and boundary discipline shown here.

## Complexity
O(n) amortized time, O(k) space.

## Code
``python
from collections import deque

def sliding_window_max(nums, k):
    dq = deque()
    result = []
    for i, value in enumerate(nums):
        while dq and dq[0] <= i - k:
            dq.popleft()
        while dq and nums[dq[-1]] <= value:
            dq.pop()
        dq.append(i)
        if i >= k - 1:
            result.append(nums[dq[0]])
    return result
``

## Edge Cases
- Empty input
- Single-element input
- Duplicate values or repeated states
- Inclusive versus exclusive boundary semantics

## Modifications
Invert comparisons for minimum; store values only when expiration can be determined separately.

## Common Interview Variations
- Return the count instead of the object
- Return all valid objects instead of the best one
- Add online updates or streaming input
- Tighten memory from O(n) to O(1) when dependencies allow it

## Common Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Infinite Loop]]


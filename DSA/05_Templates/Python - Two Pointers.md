---
type: python-template
status: stable
tags: [dsa/template, python, dsa/two-pointers]
canonical: true
related: [[Two Pointers]]
---
# Python - Two Pointers

## Purpose
Coordinate two indices over an ordered sequence or partition boundary.

## Explanation
This template captures the reusable implementation shape for [[Two Pointers]]. Keep problem-specific naming and validation in the problem page, but preserve the invariant and boundary discipline shown here.

## Complexity
O(n) time and O(1) extra space for typical scans.

## Code
``python
def two_sum_sorted(nums, target):
    left, right = 0, len(nums) - 1
    while left < right:
        total = nums[left] + nums[right]
        if total == target:
            return left, right
        if total < target:
            left += 1
        else:
            right -= 1
    return None
``

## Edge Cases
- Empty input
- Single-element input
- Duplicate values or repeated states
- Inclusive versus exclusive boundary semantics

## Modifications
Use opposite ends for sorted constraints; use slow/write pointer for in-place filtering.

## Common Interview Variations
- Return the count instead of the object
- Return all valid objects instead of the best one
- Add online updates or streaming input
- Tighten memory from O(n) to O(1) when dependencies allow it

## Common Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Infinite Loop]]


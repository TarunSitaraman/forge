---
type: python-template
status: stable
tags: [dsa/template, python, dsa/divide-and-conquer]
canonical: true
related: [[Divide and Conquer]]
---
# Python - Divide and Conquer

## Purpose
Solve independent subproblems and combine their results.

## Explanation
This template captures the reusable implementation shape for [[Divide and Conquer]]. Keep problem-specific naming and validation in the problem page, but preserve the invariant and boundary discipline shown here.

## Complexity
Use a recurrence; merge-style problems are commonly O(n log n).

## Code
``python
def merge_sort(nums):
    if len(nums) <= 1:
        return nums[:]
    mid = len(nums) // 2
    left = merge_sort(nums[:mid])
    right = merge_sort(nums[mid:])
    merged = []
    i = j = 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            merged.append(left[i]); i += 1
        else:
            merged.append(right[j]); j += 1
    merged.extend(left[i:]); merged.extend(right[j:])
    return merged
``

## Edge Cases
- Empty input
- Single-element input
- Duplicate values or repeated states
- Inclusive versus exclusive boundary semantics

## Modifications
Avoid excessive slicing in performance-sensitive code; return aggregate summaries instead of full subarrays when possible.

## Common Interview Variations
- Return the count instead of the object
- Return all valid objects instead of the best one
- Add online updates or streaming input
- Tighten memory from O(n) to O(1) when dependencies allow it

## Common Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Infinite Loop]]


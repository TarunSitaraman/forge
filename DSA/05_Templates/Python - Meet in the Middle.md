---
type: python-template
status: stable
tags: [dsa/template, python, dsa/meet-in-the-middle]
canonical: true
related: [[Meet in the Middle]]
---
# Python - Meet in the Middle

## Purpose
Enumerate two halves of an exponential search and combine summaries.

## Explanation
This template captures the reusable implementation shape for [[Meet in the Middle]]. Keep problem-specific naming and validation in the problem page, but preserve the invariant and boundary discipline shown here.

## Complexity
O(2^(n/2) log 2^(n/2)) typical time, O(2^(n/2)) space.

## Code
``python
from bisect import bisect_right

def subset_sum_at_most(nums, limit):
    mid = len(nums) // 2
    def sums(part):
        result = [0]
        for value in part:
            result += [x + value for x in result]
        return result
    left = sums(nums[:mid])
    right = sorted(sums(nums[mid:]))
    best = 0
    for value in left:
        if value <= limit:
            j = bisect_right(right, limit - value) - 1
            if j >= 0:
                best = max(best, value + right[j])
    return best
``

## Edge Cases
- Empty input
- Single-element input
- Duplicate values or repeated states
- Inclusive versus exclusive boundary semantics

## Modifications
Use counts or maps instead of sorted arrays when exact matching is needed.

## Common Interview Variations
- Return the count instead of the object
- Return all valid objects instead of the best one
- Add online updates or streaming input
- Tighten memory from O(n) to O(1) when dependencies allow it

## Common Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Infinite Loop]]


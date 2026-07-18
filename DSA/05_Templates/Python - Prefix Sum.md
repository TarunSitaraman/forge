---
type: python-template
status: stable
tags: [dsa/template, python, dsa/prefix-sum]
canonical: true
related: [[Prefix Sum]]
---
# Python - Prefix Sum

## Purpose
Precompute cumulative sums for constant-time range aggregate queries.

## Explanation
This template captures the reusable implementation shape for [[Prefix Sum]]. Keep problem-specific naming and validation in the problem page, but preserve the invariant and boundary discipline shown here.

## Complexity
O(n) build, O(1) query, O(n) space.

## Code
``python
def prefix_sums(nums):
    prefix = [0]
    for value in nums:
        prefix.append(prefix[-1] + value)
    return prefix

def range_sum(prefix, left, right):
    return prefix[right] - prefix[left]
``

## Edge Cases
- Empty input
- Single-element input
- Duplicate values or repeated states
- Inclusive versus exclusive boundary semantics

## Modifications
Use hashmap of prefix states for subarray-count problems; extend to 2D matrices with row/column inclusion-exclusion.

## Common Interview Variations
- Return the count instead of the object
- Return all valid objects instead of the best one
- Add online updates or streaming input
- Tighten memory from O(n) to O(1) when dependencies allow it

## Common Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Infinite Loop]]


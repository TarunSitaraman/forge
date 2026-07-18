---
type: python-template
status: stable
tags: [dsa/template, python, dsa/sliding-window]
canonical: true
related: [[Sliding Window]]
---
# Python - Sliding Window

## Purpose
Maintain a contiguous window with counts or aggregate state.

## Explanation
This template captures the reusable implementation shape for [[Sliding Window]]. Keep problem-specific naming and validation in the problem page, but preserve the invariant and boundary discipline shown here.

## Complexity
O(n) time when each boundary moves forward at most n times; O(k) auxiliary state.

## Code
``python
from collections import defaultdict

def longest_at_most_k_distinct(s, k):
    counts = defaultdict(int)
    left = 0
    best = 0
    for right, ch in enumerate(s):
        counts[ch] += 1
        while len(counts) > k:
            old = s[left]
            counts[old] -= 1
            if counts[old] == 0:
                del counts[old]
            left += 1
        best = max(best, right - left + 1)
    return best
``

## Edge Cases
- Empty input
- Single-element input
- Duplicate values or repeated states
- Inclusive versus exclusive boundary semantics

## Modifications
Switch the validity predicate; track sums instead of counts; use fixed-size windows when length is constant.

## Common Interview Variations
- Return the count instead of the object
- Return all valid objects instead of the best one
- Add online updates or streaming input
- Tighten memory from O(n) to O(1) when dependencies allow it

## Common Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Infinite Loop]]


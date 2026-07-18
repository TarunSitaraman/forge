---
type: problem
status: stable
tags: [dsa/problem, dsa/prefix-sum-subarray-sum-equals-k]
canonical: true
related: [[[Prefix Sum]], [[Hashing]], [[Python - Prefix Sum]]]
---
# Prefix Sum - Subarray Sum Equals K

## Problem Statement
Count subarrays whose sum equals k.

## Classification
- Patterns: [[Prefix Sum]], [[Hashing]]
- Template: [[Python - Prefix Sum]]

## Pattern Selection
Count prior prefix sums equal to current_sum - k. This page applies canonical theory rather than duplicating it.

## Python Solution
``python
from collections import defaultdict

def subarray_sum(nums, k):
    counts = defaultdict(int)
    counts[0] = 1
    total = answer = 0
    for value in nums:
        total += value
        answer += counts[total - k]
        counts[total] += 1
    return answer
``

## Reasoning
The solution preserves the invariant owned by the linked pattern page, advances monotonically through the input or state space, and records only the state required to produce the answer.

## Complexity
O(n) time and O(n) space.

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


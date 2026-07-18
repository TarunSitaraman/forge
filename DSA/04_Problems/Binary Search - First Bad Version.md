---
type: problem
status: stable
tags: [dsa/problem, dsa/binary-search-first-bad-version]
canonical: true
related: [[[Binary Search]], [[Python - Binary Search]]]
---
# Binary Search - First Bad Version

## Problem Statement
Find the first index where a monotonic predicate becomes true.

## Classification
- Patterns: [[Binary Search]]
- Template: [[Python - Binary Search]]

## Pattern Selection
This is the canonical first-true problem; the theory belongs in [[Binary Search]]. This page applies canonical theory rather than duplicating it.

## Python Solution
``python
def first_true(n, is_bad):
    lo, hi = 1, n
    while lo < hi:
        mid = lo + (hi - lo) // 2
        if is_bad(mid):
            hi = mid
        else:
            lo = mid + 1
    return lo
``

## Reasoning
The solution preserves the invariant owned by the linked pattern page, advances monotonically through the input or state space, and records only the state required to produce the answer.

## Complexity
O(log n) calls to the predicate and O(1) space.

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


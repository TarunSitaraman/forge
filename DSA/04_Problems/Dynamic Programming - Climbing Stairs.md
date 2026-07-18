---
type: problem
status: stable
tags: [dsa/problem, dsa/dynamic-programming-climbing-stairs]
canonical: true
related: [[[Dynamic Programming]], [[Memoization]], [[Python - Dynamic Programming]]]
---
# Dynamic Programming - Climbing Stairs

## Problem Statement
Count ways to reach step n with one-step or two-step moves.

## Classification
- Patterns: [[Dynamic Programming]], [[Memoization]]
- Template: [[Python - Dynamic Programming]]

## Pattern Selection
The state depends only on the previous two states, so the DP table can be compressed. This page applies canonical theory rather than duplicating it.

## Python Solution
``python
def climb_stairs(n):
    if n <= 2:
        return n
    prev2, prev1 = 1, 2
    for _ in range(3, n + 1):
        prev2, prev1 = prev1, prev1 + prev2
    return prev1
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


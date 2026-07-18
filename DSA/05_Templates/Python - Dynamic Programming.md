---
type: python-template
status: stable
tags: [dsa/template, python, dsa/dynamic-programming]
canonical: true
related: [[Dynamic Programming]]
---
# Python - Dynamic Programming

## Purpose
Store subproblem answers defined by state and transition.

## Explanation
This template captures the reusable implementation shape for [[Dynamic Programming]]. Keep problem-specific naming and validation in the problem page, but preserve the invariant and boundary discipline shown here.

## Complexity
O(states * transitions); space equals retained states.

## Code
``python
def coin_change_min(coins, amount):
    inf = amount + 1
    dp = [0] + [inf] * amount
    for total in range(1, amount + 1):
        for coin in coins:
            if total >= coin:
                dp[total] = min(dp[total], dp[total - coin] + 1)
    return -1 if dp[amount] == inf else dp[amount]
``

## Edge Cases
- Empty input
- Single-element input
- Duplicate values or repeated states
- Inclusive versus exclusive boundary semantics

## Modifications
Switch loop order for combinations versus permutations; compress dimensions only when dependencies permit it.

## Common Interview Variations
- Return the count instead of the object
- Return all valid objects instead of the best one
- Add online updates or streaming input
- Tighten memory from O(n) to O(1) when dependencies allow it

## Common Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Infinite Loop]]


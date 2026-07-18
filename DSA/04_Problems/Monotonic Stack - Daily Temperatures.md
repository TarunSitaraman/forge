---
type: problem
status: stable
tags: [dsa/problem, dsa/monotonic-stack-daily-temperatures]
canonical: true
related: [[[Monotonic Stack]], [[Python - Monotonic Stack]]]
---
# Monotonic Stack - Daily Temperatures

## Problem Statement
For each day, find days until a warmer temperature.

## Classification
- Patterns: [[Monotonic Stack]]
- Template: [[Python - Monotonic Stack]]

## Pattern Selection
Keep unresolved colder days on the stack until a warmer value resolves them. This page applies canonical theory rather than duplicating it.

## Python Solution
``python
def daily_temperatures(temperatures):
    answer = [0] * len(temperatures)
    stack = []
    for i, temp in enumerate(temperatures):
        while stack and temperatures[stack[-1]] < temp:
            j = stack.pop()
            answer[j] = i - j
        stack.append(i)
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


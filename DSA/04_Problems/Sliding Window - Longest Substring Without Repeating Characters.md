---
type: problem
status: stable
tags: [dsa/problem, dsa/sliding-window-longest-substring-without-repeating-characters]
canonical: true
related: [[[Sliding Window]], [[Hashing]], [[Python - Sliding Window]]]
---
# Sliding Window - Longest Substring Without Repeating Characters

## Problem Statement
Find the longest substring whose characters are all unique.

## Classification
- Patterns: [[Sliding Window]], [[Hashing]]
- Template: [[Python - Sliding Window]]

## Pattern Selection
The key move is shrinking only until the duplicate is removed, not restarting the window. This page applies canonical theory rather than duplicating it.

## Python Solution
``python
def length_of_longest_substring(s):
    last = {}
    left = 0
    best = 0
    for right, ch in enumerate(s):
        if ch in last and last[ch] >= left:
            left = last[ch] + 1
        last[ch] = right
        best = max(best, right - left + 1)
    return best
``

## Reasoning
The solution preserves the invariant owned by the linked pattern page, advances monotonically through the input or state space, and records only the state required to produce the answer.

## Complexity
O(n) time and O(k) space for alphabet size k.

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


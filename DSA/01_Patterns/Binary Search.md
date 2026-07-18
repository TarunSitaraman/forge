---
type: pattern
status: stable
tags: [dsa/pattern, dsa/binary-search]
canonical: true
---
# Binary Search

## When To Use
Use when the search space is sorted or the predicate is monotonic.

## Core Idea
Maintain an invariant that keeps the answer inside the active search range.

## Complexity Signals
- Time: O(log n) over a bounded ordered space
- Space: O(1) for iterative search

## Template Links
- [[Python - Binary Search]]

## Representative Problems
- [[Binary Search Representative Problems]]

## Related Patterns
- [[Two Pointers]]
- [[Prefix Sum]]

## Mistakes
- [[Wrong Mid Calculation]]
- [[Off-by-One]]
- [[Boundary Errors]]


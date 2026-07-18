---
type: problem
status: stable
tags: [dsa/problem, dsa/heap-k-closest-points]
canonical: true
related: [[[Heap]], [[Priority Queue]], [[Python - Heap]]]
---
# Heap - K Closest Points

## Problem Statement
Return k points closest to the origin.

## Classification
- Patterns: [[Heap]], [[Priority Queue]]
- Template: [[Python - Heap]]

## Pattern Selection
Use negative distance to simulate a max-heap in Python heapq. This page applies canonical theory rather than duplicating it.

## Python Solution
``python
import heapq

def k_closest(points, k):
    heap = []
    for x, y in points:
        dist = x * x + y * y
        heapq.heappush(heap, (-dist, x, y))
        if len(heap) > k:
            heapq.heappop(heap)
    return [(x, y) for _, x, y in heap]
``

## Reasoning
The solution preserves the invariant owned by the linked pattern page, advances monotonically through the input or state space, and records only the state required to produce the answer.

## Complexity
O(n log k) with a bounded max-heap; O(k) space.

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


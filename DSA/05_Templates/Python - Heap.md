---
type: python-template
status: stable
tags: [dsa/template, python]
canonical: true
---
# Python - Heap

## Purpose
Reusable interview-ready Python template for [[Heap]].

## Complexity
- Time: Depends on operation and input size; document per problem.
- Space: Depends on auxiliary structures; document per problem.

## Code
``python
import heapq

def k_smallest(nums, k):
    heap = list(nums)
    heapq.heapify(heap)
    return [heapq.heappop(heap) for _ in range(min(k, len(heap)))]
``

## Edge Cases
- Empty input
- Single-element input
- Duplicate values or repeated states
- Disconnected components when graph-shaped

## Common Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Missing Visited Set]]


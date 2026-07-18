---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/heap]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Heap Cheat Sheet

## Signals
Kth largest/smallest; top-k; merge k sorted; running median.

## Core Moves
- Python heapq is min-heap; negate for max-heap
- Kth largest: maintain min-heap of size k
- Median: two heaps (max-heap lower half, min-heap upper half)

## Complexity
O(log n) push/pop, O(n log k) for k-selection problems.

## Template Links
- [[Heapq Cheat Sheet]] (existing detailed version)

## Watch For
- Wrong heap type (min vs max)
- Not maintaining bounded size

## Canonical Depth
See [[Heap]] for full explanation.

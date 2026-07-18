---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/heap]
canonical: true
---
# Heap Representative Problems

Core problem variants that exercise Heap data structures across different selection and priority-based problems.

## Variant 1: Kth Largest/Smallest Element
**Recognition:** Find kth extreme element without full sorting.
**Mechanics:** Min-heap of size k tracks k largest; heap top is kth largest.
**Key Challenge:** When to build min-heap vs. max-heap; memory efficiency.

- [[Heap - Kth Largest Element]]
- Variant: Kth smallest
- Variant: Median of data stream (two heaps)
- Variant: Single element k times in stream

## Variant 2: Priority-Based Selection
**Recognition:** Process elements in priority order (not arrival order).
**Mechanics:** Heap organizes by priority; pop top, process, repeat.
**Key Challenge:** Defining priority correctly; handling ties.

- Variant: Frequented elements
- Variant: Top k frequent elements
- Variant: Sort by custom priority

## Variant 3: Merge Multiple Sorted Sequences
**Recognition:** Merge k sorted arrays/lists into one sorted output.
**Mechanics:** Min-heap of k elements (one from each sequence); heap operations merge efficiently.
**Key Challenge:** Tracking which sequence each element came from; O(n log k) time.

- [[Heap - Merge K Sorted Lists]]
- Variant: Merge k sorted arrays
- Variant: Smallest range covering from k lists

## Variant 4: Interval & Scheduling
**Recognition:** Process events (start/end) in order; heap manages active set.
**Mechanics:** Heap tracks active intervals; pop when done; add when starting.
**Key Challenge:** Handling overlap; correct event ordering.

- [[Heap - Meeting Rooms II]]
- Variant: Employee free time (finding gaps)
- Variant: Skyline problem (events at x-coordinates)

## Variant 5: Greedy Optimization with Heap
**Recognition:** Greedy selection of best elements in priority order.
**Mechanics:** Heap enables efficient "pick best remaining" operation.
**Key Challenge:** Proving greedy choice is optimal; heap efficiency over brute force.

- Variant: Minimize cost (connect ropes, stock trades)
- Variant: Maximize value (jump game)
- Variant: Rearrange strings (max distance between chars)

## Variant 6: Dynamic Median & Percentiles
**Recognition:** Maintain median or kth percentile as data streams in.
**Mechanics:** Max-heap for lower half; min-heap for upper half; balance.
**Key Challenge:** Coordinating two heaps; handling even/odd counts.

- [[Heap - Median Finder]]
- Variant: Sliding window median
- Variant: Percentile tracking

## Cross-Linking
- **Pattern:** [[Heap]]
- **Related Patterns:** [[Priority Queue]], [[Greedy]]
- **Data Structure:** [[Heap]] (Binary Heap implementation)
- **Templates:** [[Heapq Cheat Sheet]]
- **Interview Guide:** [[Heap Interview Guide]]

## Key Operations
| Operation | Time | Use |
|-----------|------|-----|
| **Push** | O(log n) | Add element to heap |
| **Pop** | O(log n) | Remove top element |
| **Peek** | O(1) | View top without removing |
| **Heapify** | O(n) | Build heap from array |

## Python Specifics
- `heapq` is min-heap by default
- For max-heap: negate values or use tuples with negation
- `heappush` / `heappop` / `heapify` (in-place on list)
- No decrease-key operation (inefficient for some algorithms)

## Common Pitfalls
- Using wrong heap type (max when need min)
- Forgetting Python's heapq is min-heap (negation workaround)
- O(n log n) full sort when heap achieves O(n log k)
- Not heapifying at start (if building from existing array)
- Heap property holds globally, not per-level

## Interview Red Flags
- "Kth largest" → Min-heap of size k
- "Median stream" → Two-heap balanced structure
- "Top k frequent" → Min-heap + hash map of frequencies
- "Merge sorted" → Min-heap of k elements
- "Minimum cost" → Min-heap for greedy selection


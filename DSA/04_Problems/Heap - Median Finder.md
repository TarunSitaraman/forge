---
type: problem
status: stable
tags: [dsa/problem, dsa/heap]
canonical: true
related: [[[Heap]], [[Priority Queue]]]
difficulty: hard
---
# Heap - Find Median from Data Stream

## Problem Statement
Design a data structure that supports adding integers from a stream and finding the median of all elements added so far, at any point.

**Constraints:**
- `addNum(int)` and `findMedian()` called in any order, arbitrarily many times
- Must be more efficient than sorting the whole collection on every query

## Classification
- **Patterns:** [[Heap]]
- **Category:** Two-heap balancing for streaming order statistics

## Pattern Selection
This is the canonical two-heap problem: a single heap gives fast access to one extreme (min or max), but median requires fast access to the middle. Splitting the stream into a max-heap of the lower half and a min-heap of the upper half gives O(1) median access.

## Why Two Heaps Work
**Invariant:** `max_heap` (lower half, largest on top) always holds either
the same number of elements as `min_heap` (upper half, smallest on top),
or exactly one more. The median is then either `max_heap`'s top (odd
count) or the average of both tops (even count).

**Balancing rule:** after inserting into either heap, if the size
difference exceeds 1, pop from the larger heap and push into the smaller
one to restore the invariant.

## Python Solution
```python
import heapq

class MedianFinder:
    def __init__(self):
        self.small = []  # max-heap (negated) for lower half
        self.large = []  # min-heap for upper half

    def addNum(self, num: int) -> None:
        heapq.heappush(self.small, -num)

        # Ensure every element in small <= every element in large
        heapq.heappush(self.large, -heapq.heappop(self.small))

        # Rebalance: small may hold at most one more than large
        if len(self.large) > len(self.small):
            heapq.heappush(self.small, -heapq.heappop(self.large))

    def findMedian(self) -> float:
        if len(self.small) > len(self.large):
            return -self.small[0]
        return (-self.small[0] + self.large[0]) / 2.0
```

## Reasoning
1. **Insert into `small`** (max-heap) first — simplest default.
2. **Cross-balance:** pop `small`'s max and push into `large` — guarantees
   ordering between the two heaps even if the new number belonged in
   `large`.
3. **Size-balance:** if `large` outgrew `small`, move its min back.
4. **Median read:** odd total → `small`'s top; even total → average of
   both tops.

## Complexity Analysis
- **addNum:** O(log n) — up to two heap pushes/pops
- **findMedian:** O(1) — just peek both tops
- **Space:** O(n) — all elements stored across both heaps

## Edge Cases & Validation
```python
mf = MedianFinder()
mf.addNum(1)
mf.addNum(2)
assert mf.findMedian() == 1.5
mf.addNum(3)
assert mf.findMedian() == 2

mf2 = MedianFinder()
mf2.addNum(5)
assert mf2.findMedian() == 5
```

## Common Mistakes
1. **Using one heap only:** gives fast min/max, not median — need both.
2. **Wrong balance direction:** inserting directly into whichever heap
   "seems right" without the cross-balance step breaks the invariant
   that every `small` element ≤ every `large` element.
3. **Forgetting size-balance:** without it, one heap can grow unbounded
   relative to the other, breaking the O(1) median read.

## Interview Walkthrough
**Interviewer:** "Find the median of a stream of integers, efficiently."

**Your Response:**
1. "One heap gives fast access to an extreme, but median needs the
   middle — so I'll split the stream into two heaps."
2. "Max-heap for the lower half, min-heap for the upper half, kept
   balanced in size."
3. "Insert-then-cross-balance keeps every element in the lower heap ≤
   every element in the upper heap."
4. "Median is then O(1): either the lower heap's top, or the average of
   both tops."

## Variations
1. **Sliding Window Median:** same idea but must also support removal
   (lazy deletion with a hash map of pending removals is the usual fix)
2. **Kth Order Statistic Stream:** generalizes beyond the median to any
   fixed rank

## Related Problems
- [[Heap - Top K Frequent Elements]] (bounded min-heap, different goal)
- [[Heap Representative Problems]]

## Related Concepts
- [[Heap]] — dual-heap balancing technique
- [[Priority Queue]] — underlying abstraction


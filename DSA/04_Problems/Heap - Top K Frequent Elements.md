---
type: problem
status: stable
tags: [dsa/problem, dsa/heap]
canonical: true
related: [[[Heap]], [[Hash Map]]]
difficulty: medium
---
# Heap - Top K Frequent Elements

## Problem Statement
Given an integer array and an integer k, return the k most frequent elements.

**Constraints:**
- Answer is guaranteed to be unique
- 1 ≤ k ≤ number of unique elements

## Classification
- **Patterns:** [[Heap]], [[Hashing]]
- **Category:** Frequency-based top-k selection

## Pattern Selection
This is canonical for Heap because we need the k largest (by frequency) without fully sorting all unique elements—a min-heap of size k achieves O(n log k) instead of O(n log n).

## Why Heap Works
**Key Insight:** Maintain a min-heap of size k based on frequency. If a new element's frequency exceeds the heap's minimum, replace it. Final heap contains exactly the k most frequent elements.

## Python Solution
```python
import heapq
from collections import Counter

def topKFrequent(nums: list[int], k: int) -> list[int]:
    """
    Find k most frequent elements using min-heap.
    Time: O(n log k)
    Space: O(n) for frequency map, O(k) for heap
    """
    count = Counter(nums)
    
    # Min-heap of (frequency, value) pairs, size k
    heap = []
    for num, freq in count.items():
        heapq.heappush(heap, (freq, num))
        if len(heap) > k:
            heapq.heappop(heap)  # Remove smallest frequency
    
    return [num for freq, num in heap]


def topKFrequentBucket(nums: list[int], k: int) -> list[int]:
    """
    Alternative: O(n) using bucket sort (frequency bounded by n).
    Time: O(n) - no heap needed since frequency has bounded range
    Space: O(n)
    """
    count = Counter(nums)
    buckets = [[] for _ in range(len(nums) + 1)]
    
    for num, freq in count.items():
        buckets[freq].append(num)
    
    result = []
    for freq in range(len(buckets) - 1, 0, -1):
        for num in buckets[freq]:
            result.append(num)
            if len(result) == k:
                return result
    
    return result
```

## Reasoning
1. **Count Frequencies:** Use Counter for O(n) frequency counting
2. **Maintain Min-Heap of Size K:** Push (frequency, value) pairs; if heap exceeds k, pop smallest
3. **Result:** Heap contains exactly k most frequent elements
4. **Bucket Sort Alternative:** Since max frequency ≤ n, use buckets indexed by frequency for O(n) solution

## Complexity Analysis
- **Heap Approach:** O(n log k) time, O(n+k) space
- **Bucket Sort Approach:** O(n) time, O(n) space (better but less commonly expected in interviews)

## Edge Cases & Validation
```python
assert sorted(topKFrequent([1,1,1,2,2,3], 2)) == [1,2]
assert topKFrequent([1], 1) == [1]
assert sorted(topKFrequent([1,2], 2)) == [1,2]
```

## Common Mistakes
1. **Using Max-Heap of All Elements:** O(n log n) instead of O(n log k) — misses the size-k optimization
2. **Wrong Heap Comparison:** Storing (value, freq) instead of (freq, value) breaks heap ordering by frequency
3. **Not Removing When Heap Exceeds K:** Heap grows unbounded, losing the efficiency benefit

## Interview Walkthrough
**Interviewer:** "Find k most frequent elements."

**Your Response:**
1. "First, count frequencies using a hash map—O(n)."
2. "Then maintain a min-heap of size k based on frequency."
3. "If a new element's frequency exceeds heap minimum, swap it in."
4. "This gives O(n log k) instead of O(n log n) full sort."
5. "Alternative: bucket sort by frequency achieves O(n) since frequency is bounded by array length."

## Related Problems
- [[Heap - K Closest Points]] (similar top-k pattern, different criteria)
- [[Heap Representative Problems]]

## Related Concepts
- [[Heap]] — bounded size for efficient top-k
- [[Hash Map]] — frequency counting


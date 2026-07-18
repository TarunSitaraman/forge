---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/heap]
canonical: true
related: [[[Heap]], [[Coding Interviews]], [[Communicating Thought Process]]]
---
# Heap Interview Guide

## Pattern Recognition (30 seconds)

**Green Flags:**
- "Kth largest/smallest" element
- "Top k" frequent/closest elements
- Merge k sorted structures
- Running median or priority-based processing

## Communication Template

```
Interviewer: "Find kth largest element in stream."

You: "I'll maintain a min-heap of size k. If heap exceeds k, pop 
smallest. The heap's top is always the kth largest seen so far.
This gives O(log k) per insertion instead of O(n log n) full sort."
```

## Core Mechanics

```python
import heapq

def kth_largest(nums, k):
    heap = []
    for num in nums:
        heapq.heappush(heap, num)
        if len(heap) > k:
            heapq.heappop(heap)  # Remove smallest
    return heap[0]  # Kth largest is at top of min-heap
```

## Common Mistakes

1. **Wrong heap type:** Python's heapq is min-heap; negate values for max-heap behavior
2. **Not maintaining size k:** Forgetting to pop when heap exceeds k
3. **O(n log n) when O(n log k) possible:** Full sort instead of bounded heap

## Practice Problems
- Kth Largest Element
- Top K Frequent Elements
- Merge K Sorted Lists
- Find Median from Data Stream

## The Checklist
- [ ] Chose correct heap type (min vs max) for the problem
- [ ] Maintained bounded heap size where applicable
- [ ] Confirmed O(n log k) instead of O(n log n) where possible

## Related Content
- [[Heap]] — Pattern deep dive
- [[Heap Representative Problems]]


---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/monotonic-queue]
canonical: true
related: [[[Monotonic Queue]], [[Coding Interviews]]]
---
# Monotonic Queue Interview Guide

## Pattern Recognition
**Green Flags:** Sliding window maximum/minimum; need O(1) amortized access to window extremum

## Communication Template
`I'll maintain a deque storing indices in decreasing value order. When window slides, remove indices outside window from front, and remove smaller values from back before adding new element.`

## Core Mechanics
```python
from collections import deque

def max_sliding_window(nums, k):
    dq = deque()  # stores indices, values decreasing
    result = []
    for i, num in enumerate(nums):
        while dq and nums[dq[-1]] < num:
            dq.pop()
        dq.append(i)
        if dq[0] <= i - k:
            dq.popleft()
        if i >= k - 1:
            result.append(nums[dq[0]])
    return result
```

## Common Mistakes
1. Storing values instead of indices (can't check window boundary)
2. Wrong comparison direction for max vs min queue
3. Not removing out-of-window indices from front

## Practice Problems
- Sliding Window Maximum, Shortest Subarray with Sum at Least K

## Checklist
- [ ] Stores indices, not values
- [ ] Correct monotonic direction for max/min
- [ ] Removes out-of-window elements from front

## Related
- [[Monotonic Queue]]
- [[Monotonic Queue Representative Problems]]

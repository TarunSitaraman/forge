---
type: problem
status: stable
tags: [dsa/problem, dsa/monotonic-queue]
canonical: true
related: [[[Monotonic Queue]], [[Sliding Window]]]
difficulty: hard
---
# Monotonic Queue - Sliding Window Maximum

## Problem Statement
Given an array and window size k, return the maximum value in each sliding window as it moves from left to right.

**Constraints:**
- 1 ≤ k ≤ nums.length
- Must achieve better than O(n*k) brute force

## Classification
- **Patterns:** [[Monotonic Queue]], [[Sliding Window]]
- **Category:** Efficient window extremum tracking

## Pattern Selection
This is canonical for Monotonic Queue because it needs O(1) amortized access to window maximum, achieved via a deque maintaining decreasing order.

## Why Monotonic Queue Works
**Key Insight:** Maintain deque of indices where corresponding values are in decreasing order. Front of deque is always the current window's maximum. When new element arrives, remove all smaller elements from back (they can never be the max while newer larger element exists).

## Python Solution
```python
from collections import deque

def maxSlidingWindow(nums: list[int], k: int) -> list[int]:
    """
    Find maximum in each sliding window using monotonic deque.
    Time: O(n) - each element added/removed once
    Space: O(k) for deque
    """
    dq = deque()  # Stores indices, values in decreasing order
    result = []
    
    for i, num in enumerate(nums):
        # Remove indices outside current window
        while dq and dq[0] <= i - k:
            dq.popleft()
        
        # Remove indices with smaller values (they're useless now)
        while dq and nums[dq[-1]] < num:
            dq.pop()
        
        dq.append(i)
        
        # Once window is full, record maximum
        if i >= k - 1:
            result.append(nums[dq[0]])
    
    return result
```

## Reasoning
1. **Maintain Decreasing Deque:** Only keep indices whose values could still be the maximum
2. **Remove Out-of-Window:** Front of deque removed if outside current window bounds
3. **Remove Smaller Values:** Back of deque removed if smaller than incoming element (they're dominated)
4. **Front is Always Max:** After maintenance, front of deque holds current window's maximum

## Complexity Analysis
- **Time:** O(n) — each element added once, removed at most once (amortized)
- **Space:** O(k) — deque size bounded by window size

## Edge Cases & Validation
```python
assert maxSlidingWindow([1,3,-1,-3,5,3,6,7], 3) == [3,3,5,5,6,7]
assert maxSlidingWindow([1], 1) == [1]
assert maxSlidingWindow([9,11], 2) == [11]
```

## Common Mistakes
1. **Storing Values Instead of Indices:** Can't check window boundary without index
2. **Wrong Removal Order:** Must check out-of-window BEFORE checking smaller values
3. **Not Maintaining Monotonic Property:** Forgetting to pop smaller values from back

## Interview Walkthrough
**Interviewer:** "Find maximum in each sliding window, better than O(n*k)."

**Your Response:**
1. "I'll maintain a deque storing indices with decreasing values."
2. "New element arrives: pop smaller values from back (they're dominated), then add new index."
3. "Front of deque is always current maximum after removing out-of-window indices."
4. "This achieves O(n) since each element is added/removed from deque exactly once."

## Related Problems
- [[Monotonic Queue Representative Problems]]

## Related Concepts
- [[Monotonic Queue]] — core technique
- [[Sliding Window]] — broader window context


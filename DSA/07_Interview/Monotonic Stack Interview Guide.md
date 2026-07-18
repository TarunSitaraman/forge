---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/monotonic-stack]
canonical: true
related: [[[Monotonic Stack]], [[Coding Interviews]]]
---
# Monotonic Stack Interview Guide

## Pattern Recognition
**Green Flags:** `Next greater/smaller element`, `daily temperatures`, `largest rectangle in histogram`, span problems

## Communication Template
`I'll maintain a monotonic stack. When current element breaks the monotonic property, I pop and resolve those elements - each pop tells me the 'next greater/smaller' for that popped index.`

## Core Mechanics
```python
def next_greater(nums):
    stack = []  # stores indices
    result = [-1] * len(nums)
    for i, num in enumerate(nums):
        while stack and nums[stack[-1]] < num:
            idx = stack.pop()
            result[idx] = num
        stack.append(i)
    return result
```

## Common Mistakes
1. Wrong monotonic direction (increasing vs decreasing stack)
2. Storing values instead of indices (loses position info)
3. Not handling empty stack after popping

## Practice Problems
- Daily Temperatures, Largest Rectangle in Histogram, Next Greater Element

## Checklist
- [ ] Correct stack monotonicity for problem type
- [ ] Store indices, not values, when position matters
- [ ] O(n) time verified (each element pushed/popped once)

## Related
- [[Monotonic Stack]]
- [[Monotonic Stack Representative Problems]]

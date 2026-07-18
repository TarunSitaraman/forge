---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/difference-array]
canonical: true
related: [[[Difference Array]], [[Coding Interviews]]]
---
# Difference Array Interview Guide

## Pattern Recognition
**Green Flags:** Multiple range updates then single final query; range increment/decrement operations

## Communication Template
`Instead of updating every element in range [i,j] (O(n) per update), I mark diff[i] += val and diff[j+1] -= val (O(1) per update). Final prefix sum gives actual values.`

## Core Mechanics
```python
def range_updates(n, updates):
    diff = [0] * (n + 1)
    for start, end, val in updates:
        diff[start] += val
        diff[end + 1] -= val
    
    result = [0] * n
    result[0] = diff[0]
    for i in range(1, n):
        result[i] = result[i-1] + diff[i]
    return result
```

## Common Mistakes
1. Off-by-one at range boundary (end vs end+1)
2. Forgetting to compute final prefix sum (just having diff array isn't the answer)
3. Not extending diff array by 1 for the end+1 boundary marker

## Practice Problems
- Range Addition, Car Pooling, Corporate Flight Bookings

## Checklist
- [ ] Correct boundary marking (end+1, not end)
- [ ] Final prefix sum computed to get actual values
- [ ] O(n+m) complexity for n elements, m updates

## Related
- [[Difference Array]]
- [[Difference Array Representative Problems]]

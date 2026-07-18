---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/divide-and-conquer]
canonical: true
related: [[[Divide and Conquer]], [[Coding Interviews]]]
---
# Divide and Conquer Interview Guide

## Pattern Recognition
**Green Flags:** Problem naturally splits into independent subproblems; merge step combines results; sorting/searching variants

## Communication Template
`I'll divide the problem into two halves, solve each recursively, then combine results in the merge step. This gives O(n log n) if merge is O(n).`

## Core Mechanics
```python
def divide_conquer(arr):
    if len(arr) <= 1:
        return arr
    mid = len(arr) // 2
    left = divide_conquer(arr[:mid])
    right = divide_conquer(arr[mid:])
    return combine(left, right)
```

## Common Mistakes
1. Expensive combine step defeats the purpose (should be efficient)
2. Unbalanced division (not truly halving problem size)
3. Missing base case leading to infinite recursion

## Practice Problems
- Merge Sort, Quick Sort, Closest Pair of Points

## Checklist
- [ ] Combine step is efficient (not O(n^2))
- [ ] Division roughly balances subproblem sizes
- [ ] Complexity matches T(n) = 2T(n/2) + O(n) pattern

## Related
- [[Divide and Conquer]]
- [[Divide and Conquer Representative Problems]]

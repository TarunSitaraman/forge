---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/sorting]
canonical: true
related: [[[Sorting]], [[Coding Interviews]]]
---
# Sorting Interview Guide

## Pattern Recognition
**Green Flags:** Need ordered output; custom comparator; partial sort (kth element); non-comparison sort applicable (bounded integer range)

## Communication Template
`For general comparison-based sorting, I'd use merge sort for guaranteed O(n log n). If values are bounded integers, counting sort achieves O(n+k).`

## Core Mechanics
```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    return merge(left, right)

def merge(left, right):
    result = []
    i = j = 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i]); i += 1
        else:
            result.append(right[j]); j += 1
    result.extend(left[i:])
    result.extend(right[j:])
    return result
```

## Common Mistakes
1. Using unstable sort when stability required
2. Missing custom comparator for complex objects
3. Not considering non-comparison sorts for bounded integer ranges

## Practice Problems
- Merge/Quick Sort implementation, Sort Colors, Largest Number

## Checklist
- [ ] Confirmed stability requirement
- [ ] Chose appropriate algorithm for constraints
- [ ] Verified O(n log n) or better where applicable

## Related
- [[Sorting]]
- [[Sorting Representative Problems]]

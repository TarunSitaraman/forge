---
type: problem
status: stable
tags: [dsa/problem, dsa/recursion, dsa/divide-and-conquer]
canonical: true
related: [[[Recursion]], [[Divide and Conquer]]]
difficulty: medium
---
# Recursion - Merge Sort

## Problem Statement
Sort an array using merge sort algorithm, demonstrating classic divide-and-conquer recursion.

**Constraints:**
- Must achieve O(n log n) time
- Stable sort required (equal elements maintain relative order)

## Classification
- **Patterns:** [[Recursion]], [[Divide and Conquer]]
- **Category:** Foundational recursive sorting algorithm

## Pattern Selection
This is canonical for Recursion because it demonstrates the core divide-conquer-combine structure clearly: split array in half, recursively sort each half, merge sorted halves.

## Python Solution
```python
def mergeSort(arr: list[int]) -> list[int]:
    """
    Sort array using recursive merge sort.
    Time: O(n log n)
    Space: O(n) for temporary arrays during merge
    """
    if len(arr) <= 1:
        return arr  # Base case: single element is sorted
    
    mid = len(arr) // 2
    left = mergeSort(arr[:mid])
    right = mergeSort(arr[mid:])
    
    return merge(left, right)

def merge(left: list[int], right: list[int]) -> list[int]:
    """Merge two sorted arrays into one sorted array."""
    result = []
    i = j = 0
    
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:  # <= ensures stability
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    
    result.extend(left[i:])
    result.extend(right[j:])
    return result
```

## Reasoning
1. **Base Case:** Array of length 0 or 1 is trivially sorted
2. **Divide:** Split array into two roughly equal halves
3. **Conquer:** Recursively sort each half
4. **Combine:** Merge two sorted halves into one sorted result

## Complexity Analysis
- **Time:** O(n log n) — log n levels of recursion, O(n) work per level for merging
- **Space:** O(n) — temporary arrays for merging (plus O(log n) recursion stack)

## Edge Cases & Validation
```python
assert mergeSort([]) == []
assert mergeSort([1]) == [1]
assert mergeSort([3,1,2]) == [1,2,3]
assert mergeSort([5,4,3,2,1]) == [1,2,3,4,5]
assert mergeSort([1,1,1]) == [1,1,1]
```

## Common Mistakes
1. **Using `<` Instead of `<=` in Merge:** Breaks stability guarantee
2. **Not Handling Remaining Elements:** Forgetting `extend()` for leftover elements after one side exhausted
3. **Missing Base Case:** Infinite recursion without proper termination

## Interview Walkthrough
**Interviewer:** "Implement merge sort."

**Your Response:**
1. "Base case: single element or empty array is already sorted."
2. "Recursively split in half, sort each half, then merge."
3. "Merge step compares front elements of each half, takes smaller, repeats."
4. "Using <= instead of < preserves stability for equal elements."

## Related Problems
- [[Recursion Representative Problems]]

## Related Concepts
- [[Recursion]] — divide-conquer-combine structure
- [[Divide and Conquer]] — broader pattern this exemplifies


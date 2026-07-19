---
type: problem
status: stable
tags: [dsa/problem, dsa/divide-and-conquer]
canonical: true
related: [[[Divide and Conquer]], [[Quick Sort]]]
difficulty: medium
---
# Divide and Conquer - Kth Largest Element (Quickselect)

## Problem Statement
Given an unsorted array, find the kth largest element without fully sorting the array.

**Constraints:**
- Must beat O(n log n) full sort in average case
- k is guaranteed valid (1 ≤ k ≤ array length)

## Classification
- **Patterns:** [[Divide and Conquer]]
- **Category:** Partition-based selection (Quickselect algorithm)

## Pattern Selection
This is canonical for Divide and Conquer because Quickselect only recurses into ONE partition (unlike quicksort which recurses into both), achieving O(n) average time instead of O(n log n).

## Why Quickselect Works
**Key Insight:** After partitioning around a pivot, we know the pivot's final sorted position. If that position equals what we're looking for, we're done. Otherwise, we only need to recurse into the half that contains our target position—never both halves.

## Python Solution
```python
import random

def findKthLargest(nums: list[int], k: int) -> int:
    """
    Find kth largest element using Quickselect.
    Time: O(n) average, O(n^2) worst case
    Space: O(1) if using Lomuto partition in-place
    """
    target_index = len(nums) - k  # Convert to kth smallest position (0-indexed)
    
    def partition(left, right, pivot_index):
        pivot_value = nums[pivot_index]
        nums[pivot_index], nums[right] = nums[right], nums[pivot_index]
        
        store_index = left
        for i in range(left, right):
            if nums[i] < pivot_value:
                nums[store_index], nums[i] = nums[i], nums[store_index]
                store_index += 1
        
        nums[right], nums[store_index] = nums[store_index], nums[right]
        return store_index
    
    def quickselect(left, right):
        if left == right:
            return nums[left]
        
        pivot_index = random.randint(left, right)  # Randomize to avoid worst case
        pivot_index = partition(left, right, pivot_index)
        
        if pivot_index == target_index:
            return nums[pivot_index]
        elif pivot_index < target_index:
            return quickselect(pivot_index + 1, right)
        else:
            return quickselect(left, pivot_index - 1)
    
    return quickselect(0, len(nums) - 1)
```

## Reasoning
1. **Convert Target:** kth largest = (n-k)th smallest in 0-indexed sorted order
2. **Partition:** Choose random pivot, partition array so smaller elements are left, larger are right
3. **Compare Position:** If pivot's final position matches target, return it
4. **Recurse ONE Side Only:** Unlike quicksort, only recurse into the half containing our target—this is THE key efficiency insight

## Complexity Analysis
- **Time:** O(n) average — each recursive call processes roughly half the remaining elements, giving n + n/2 + n/4... = O(n)
- **Time:** O(n²) worst case — if consistently bad pivot choices (mitigated by randomization)
- **Space:** O(1) — in-place partitioning (excluding O(log n) recursion stack average case)

## Edge Cases & Validation
```python
assert findKthLargest([3,2,1,5,6,4], 2) == 5
assert findKthLargest([3,2,3,1,2,4,5,5,6], 4) == 4
assert findKthLargest([1], 1) == 1
```

## Common Mistakes
1. **Recursing Into Both Halves:** This turns Quickselect into Quicksort, losing the O(n) average benefit
2. **Not Randomizing Pivot:** Without randomization, adversarial input (already sorted) causes O(n²) consistently
3. **Off-by-One in Target Index:** Confusing kth largest with kth smallest position conversion

## Interview Walkthrough
**Interviewer:** "Find kth largest element, better than full sort."

**Your Response:**
1. "Full sort is O(n log n)—I can do better with Quickselect."
2. "Like quicksort, I partition around a pivot, but I only recurse into ONE side—whichever contains my target position."
3. "This gives O(n) average time instead of O(n log n)."
4. "I randomize pivot selection to avoid worst-case O(n²) on adversarial input."

**Why This Resonates:**
- Shows the key distinction from quicksort (one-side recursion vs. both-side)
- Explains why average case is O(n) with the geometric series argument
- Demonstrates awareness of worst-case mitigation via randomization

## Variations
1. **Kth Smallest:** Same algorithm, adjust target_index calculation
2. **Top K Elements (Not Just Kth):** After partitioning, all elements left of target position are the answer set
3. **Median Finding:** Special case where k = n/2

## Related Problems
- [[Heap - Top K Frequent Elements]] (alternative heap-based approach for similar problems)
- [[Divide and Conquer Representative Problems]]

## Related Concepts
- [[Divide and Conquer]] — single-side recursion optimization
- [[Quick Sort]] — related partitioning technique


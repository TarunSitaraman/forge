---
type: problem
status: stable
tags: [dsa/problem, dsa/sorting, dsa/two-pointers]
canonical: true
related: [[[Sorting]], [[Two Pointers]]]
difficulty: medium
---
# Sorting - Sort Colors (Dutch National Flag)

## Problem Statement
Given an array with n objects colored red, white, or blue (represented as 0, 1, 2), sort them in-place so objects of the same color are adjacent, in order red, white, blue. Must solve without using a sorting library, in one pass.

**Constraints:**
- Only 3 distinct values (0, 1, 2)
- One-pass, in-place required (O(1) extra space)

## Classification
- **Patterns:** [[Sorting]], [[Two Pointers]]
- **Category:** Three-way partitioning (Dutch National Flag algorithm)

## Pattern Selection
This is canonical for demonstrating that a specialized O(n) single-pass algorithm can outperform general O(n log n) sorting when the value range is known and bounded (only 3 distinct values).

## Why This Approach Works
**Key Insight:** Maintain three pointers: `low` (boundary for 0s), `mid` (current element being examined), `high` (boundary for 2s). Partition array into three regions in a single pass using swaps.

## Python Solution
```python
def sortColors(nums: list[int]) -> None:
    """
    Sort array of 0s, 1s, 2s in-place using Dutch National Flag algorithm.
    Time: O(n) single pass
    Space: O(1)
    """
    low, mid, high = 0, 0, len(nums) - 1
    
    while mid <= high:
        if nums[mid] == 0:
            nums[low], nums[mid] = nums[mid], nums[low]
            low += 1
            mid += 1
        elif nums[mid] == 1:
            mid += 1
        else:  # nums[mid] == 2
            nums[mid], nums[high] = nums[high], nums[mid]
            high -= 1
            # Note: don't increment mid here! Need to check swapped-in value
```

## Reasoning
1. **Three Pointers:** low (next position for 0), mid (current scan), high (next position for 2)
2. **Case 0:** Swap with low boundary, advance both low and mid (swapped value is already processed 0 or 1)
3. **Case 1:** Already in correct region, just advance mid
4. **Case 2:** Swap with high boundary, advance ONLY high (swapped-in value from high needs re-examination)
5. **Termination:** When mid exceeds high, all elements partitioned

## Complexity Analysis
- **Time:** O(n) — single pass, each element visited/swapped constant number of times
- **Space:** O(1) — only three pointer variables

## Edge Cases & Validation
```python
nums = [2,0,2,1,1,0]
sortColors(nums)
assert nums == [0,0,1,1,2,2]

nums2 = [2,0,1]
sortColors(nums2)
assert nums2 == [0,1,2]

nums3 = [0]
sortColors(nums3)
assert nums3 == [0]
```

## Common Mistakes
1. **Incrementing mid After Swapping with High:** This is THE classic bug—swapped-in value from high position hasn't been examined yet, must re-check it
2. **Wrong Pointer Initialization:** high should start at len(nums)-1, not len(nums)
3. **Confusing Low/Mid Advancement:** When swapping with low, BOTH low and mid advance (since low's original position held 0 or 1, already processed)

## Interview Walkthrough
**Interviewer:** "Sort array of 0s, 1s, 2s in one pass."

**Your Response:**
1. "This is the Dutch National Flag problem—three-way partitioning."
2. "I maintain three pointers: low for next 0 position, high for next 2 position, mid for current scan."
3. "Critical detail: when swapping mid with high, I don't advance mid—the swapped-in value needs examination."
4. "When swapping mid with low, I DO advance both, since the swapped-out value from low is already known to be 0 or 1."

**Why This Resonates:**
- Shows precise understanding of the tricky pointer advancement rules
- Demonstrates why single-pass O(n) beats general O(n log n) sorting here

## Variations
1. **K Colors (Generalized):** Extends to k distinct values, but Dutch flag technique doesn't directly generalize (would need counting sort)
2. **Sort Array By Parity:** Similar two-pointer partition, simpler (only 2 categories)

## Related Problems
- [[Two Pointers - Remove Duplicates]] (different two-pointer application)
- [[Sorting Representative Problems]]

## Related Concepts
- [[Sorting]] — specialized bounded-range algorithm
- [[Two Pointers]] — multi-pointer partitioning technique


---
type: problem
status: stable
tags: [dsa/problem, dsa/meet-in-middle]
canonical: true
related: [[[Meet in the Middle]], [[Hash Map]]]
difficulty: hard
---
# Meet in the Middle - Split Subset Sum

## Problem Statement
Given an array of up to 40 elements and a target sum, determine if any subset sums to exactly the target.

**Constraints:**
- n up to 40 (too large for O(2^n) brute force, but small enough for O(2^(n/2)))
- Elements can be positive, negative, or zero

## Classification
- **Patterns:** [[Meet in the Middle]]
- **Category:** Exponential problem size reduction via splitting

## Pattern Selection
This is canonical for Meet in the Middle because n=40 makes O(2^40) infeasible but O(2^20) very feasible (~1M operations), achieved by splitting into two halves.

## Why Meet in the Middle Works
**Key Insight:** Split array into two halves of size n/2. Generate all possible subset sums for each half (2^(n/2) each). For each sum in first half, check if (target - sum) exists in second half's sums using a hash set.

## Python Solution
```python
def canPartitionSubset(nums: list[int], target: int) -> bool:
    """
    Check if subset sums to target using meet-in-middle.
    Time: O(2^(n/2)) instead of O(2^n)
    Space: O(2^(n/2))
    """
    n = len(nums)
    half = n // 2
    left, right = nums[:half], nums[half:]
    
    def allSums(arr):
        sums = {0}
        for num in arr:
            sums |= {s + num for s in sums}
        return sums
    
    left_sums = allSums(left)
    right_sums = allSums(right)
    
    for s in left_sums:
        if (target - s) in right_sums:
            return True
    
    return False
```

## Reasoning
1. **Split Array:** Divide into two halves of roughly equal size
2. **Generate All Sums:** For each half, compute all possible subset sums (2^(n/2) each)
3. **Combine via Hash Set:** For each left sum, check if complementary right sum exists
4. **Efficiency:** O(2^(n/2)) total instead of O(2^n)

## Complexity Analysis
- **Time:** O(2^(n/2)) — generating sums for each half
- **Space:** O(2^(n/2)) — storing all subset sums

## Edge Cases & Validation
```python
assert canPartitionSubset([1,2,3,4,5], 9) == True  # e.g., 4+5 or 1+3+5
assert canPartitionSubset([1,2,3], 100) == False
assert canPartitionSubset([], 0) == True  # Empty subset sums to 0
```

## Common Mistakes
1. **Not Splitting Evenly:** Unbalanced split reduces the benefit (should be as close to n/2 as possible)
2. **O(n) Lookup Instead of Hash Set:** Using list instead of set for right_sums degrades to O(2^n) effectively
3. **Missing Empty Subset:** Sum of 0 (empty subset) is always valid, must include in base case

## Interview Walkthrough
**Interviewer:** "Check if subset sums to target, n up to 40."

**Your Response:**
1. "O(2^40) is too slow, but O(2^20) per half is very feasible (~1M operations)."
2. "I'll split array into two halves, generate all subset sums for each."
3. "For each sum in first half, check if complementary sum exists in second half via hash set."
4. "This achieves O(2^(n/2)) instead of O(2^n)."

## Related Problems
- [[Meet in the Middle Representative Problems]]

## Related Concepts
- [[Meet in the Middle]] — core technique
- [[Hash Map]] — enables O(1) complement lookup


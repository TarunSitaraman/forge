---
type: problem
status: stable
tags: [dsa/problem, dsa/prefix-sum]
canonical: true
related: [[[Prefix Sum]]]
difficulty: medium
---
# Prefix Sum - Product of Array Except Self

## Problem Statement
Given an array nums, return an array where result[i] equals the product of all elements except nums[i]. Must run in O(n) without using division.

**Constraints:**
- No division operator allowed
- O(n) time required
- O(1) extra space (excluding output array) for optimal solution

## Classification
- **Patterns:** [[Prefix Sum]]
- **Category:** Prefix/suffix product technique (generalization of prefix sum)

## Pattern Selection
This is canonical for the Prefix Sum pattern's generalization: instead of prefix SUMS, we use prefix PRODUCTS (left) combined with suffix products (right) to get "everything except current position" in O(1) per position after O(n) preprocessing.

## Why This Approach Works
**Key Insight:** result[i] = (product of all elements to the left of i) × (product of all elements to the right of i). Compute left products in one pass, right products in another pass (or combine cleverly for O(1) space).

## Python Solution
```python
def productExceptSelf(nums: list[int]) -> list[int]:
    """
    Compute product except self using prefix/suffix products.
    Time: O(n)
    Space: O(1) excluding output array
    """
    n = len(nums)
    result = [1] * n
    
    # First pass: result[i] = product of all elements to the LEFT of i
    left_product = 1
    for i in range(n):
        result[i] = left_product
        left_product *= nums[i]
    
    # Second pass: multiply by product of all elements to the RIGHT of i
    right_product = 1
    for i in range(n - 1, -1, -1):
        result[i] *= right_product
        right_product *= nums[i]
    
    return result
```

## Reasoning
1. **Left Pass:** result[i] accumulates product of everything BEFORE index i
2. **Right Pass:** Multiply result[i] by product of everything AFTER index i
3. **Combine:** result[i] = left_product × right_product = product of all except nums[i]
4. **No Division Needed:** Avoids issues with zeros in the array (division would fail)

## Complexity Analysis
- **Time:** O(n) — two linear passes
- **Space:** O(1) — excluding output array, only using running product variables

## Edge Cases & Validation
```python
assert productExceptSelf([1,2,3,4]) == [24,12,8,6]
assert productExceptSelf([-1,1,0,-3,3]) == [0,0,9,0,0]
assert productExceptSelf([2,3]) == [3,2]
```

## Common Mistakes
1. **Using Division:** `total_product / nums[i]` fails when array contains 0
2. **Not Combining Left/Right Efficiently:** Using separate arrays for left and right products wastes O(n) extra space when O(1) is achievable
3. **Wrong Pass Order:** Must compute left products completely before starting right pass (or interleave carefully)

## Interview Walkthrough
**Interviewer:** "Compute product except self, no division."

**Your Response:**
1. "Division would be simple but fails with zeros in the array."
2. "Instead, I'll compute prefix products (left of i) and suffix products (right of i) separately."
3. "First pass: result[i] = product of everything before i."
4. "Second pass: multiply result[i] by product of everything after i."
5. "This achieves O(n) time, O(1) extra space (output array doesn't count)."

**Why This Resonates:**
- Shows the elegant generalization of prefix sum to prefix product
- Explains explicitly why division approach fails (zeros)
- Demonstrates space optimization by combining passes cleverly

## Variations
1. **With Division Allowed:** Simpler but must handle zero-count edge cases (0, 1, or 2+ zeros change logic)
2. **Maximum Product Subarray:** Different problem tracking running max/min products (handles sign flips)

## Related Problems
- [[Prefix Sum - Subarray Sum Equals K]] (sum-based rather than product-based)
- [[Prefix Sum Representative Problems]]

## Related Concepts
- [[Prefix Sum]] — generalizes to prefix product here
- [[Array]] — in-place computation technique


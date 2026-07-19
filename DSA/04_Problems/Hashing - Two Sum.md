---
type: problem
status: stable
tags: [dsa/problem, dsa/hashing]
canonical: true
related: [[[Hashing]], [[Hash Map]]]
difficulty: easy
---
# Hashing - Two Sum

## Problem Statement
Given an array of integers and a target, return indices of the two numbers that add up to target. Each input has exactly one solution; may not use the same element twice.

**Constraints:**
- Exactly one valid answer exists
- 2 ≤ nums.length ≤ 10^4

## Classification
- **Patterns:** [[Hashing]]
- **Category:** Complement lookup via hash map

## Pattern Selection
This is the canonical introduction to Hashing because it demonstrates the fundamental "complement lookup" technique: instead of checking all pairs (O(n²)), store seen values and check for the complement in O(1).

## Why Hashing Works
**Key Insight:** For each number, the required complement (target - num) can be checked in O(1) if we've stored previously seen numbers in a hash map.

## Python Solution
```python
def twoSum(nums: list[int], target: int) -> list[int]:
    """
    Find indices of two numbers summing to target.
    Time: O(n) single pass
    Space: O(n) for hash map
    """
    seen = {}  # value -> index
    
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
    
    return []  # No solution (shouldn't happen per constraints)
```

## Reasoning
1. **Single Pass:** Iterate through array once
2. **Check Before Insert:** For each number, check if its complement was already seen
3. **Store After Check:** Add current number to hash map AFTER checking (avoids self-matching)
4. **Return Immediately:** Once complement found, return both indices

## Complexity Analysis
- **Time:** O(n) — single pass, O(1) average hash operations
- **Space:** O(n) — hash map storing up to n elements

## Edge Cases & Validation
```python
assert twoSum([2,7,11,15], 9) == [0,1]
assert twoSum([3,2,4], 6) == [1,2]
assert twoSum([3,3], 6) == [0,1]  # Duplicate values, different indices
```

## Common Mistakes
1. **Checking After Insert:** Causes self-matching if num appears once but target = 2*num
2. **Using Value as Both Key/Value Incorrectly:** Must map value→index, not index→value, for O(1) complement lookup
3. **Nested Loop O(n²):** Missing the hash map optimization entirely

## Interview Walkthrough
**Interviewer:** "Find two numbers summing to target."

**Your Response:**
1. "Brute force is O(n²)—checking all pairs."
2. "I'll use a hash map: for each number, check if its complement was already seen."
3. "Critical detail: check for complement BEFORE adding current number, to avoid self-matching."
4. "This achieves O(n) time with O(n) space trade-off."

## Variations
1. **Two Sum II (Sorted Array):** Use two pointers instead (O(1) space)
2. **Three Sum:** Fix one element, two-sum on remainder
3. **Two Sum Closest:** Find pair closest to target (different comparison logic)

## Related Problems
- [[Hashing - Group Anagrams]] (different hashing application)
- [[Hashing Representative Problems]]

## Related Concepts
- [[Hashing]] — complement lookup technique
- [[Hash Map]] — O(1) average operations


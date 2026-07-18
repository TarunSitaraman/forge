---
type: problem
status: stable
tags: [dsa/problem, dsa/sorting]
canonical: true
related: [[[Sorting]]]
difficulty: medium
---
# Sorting - Largest Number

## Problem Statement
Given a list of non-negative integers, arrange them to form the largest possible number (as a string).

**Constraints:**
- Result may be very large (return as string)
- Handle edge case of all zeros (result should be "0", not "000")

## Classification
- **Patterns:** [[Sorting]]
- **Category:** Custom comparator sorting

## Pattern Selection
This is canonical for custom-comparator sorting because standard numeric or lexicographic ordering doesn't produce the correct arrangement—need a specialized concatenation-based comparator.

## Why Custom Comparator Works
**Key Insight:** For two numbers a, b (as strings), compare "a+b" vs "b+a" to determine which order produces a larger combined number.

## Python Solution
```python
from functools import cmp_to_key

def largestNumber(nums: list[int]) -> str:
    """
    Arrange numbers to form largest possible number.
    Time: O(n log n) for sorting
    Space: O(n) for string conversions
    """
    strs = [str(n) for n in nums]
    
    def compare(a, b):
        if a + b > b + a:
            return -1  # a should come first
        elif a + b < b + a:
            return 1   # b should come first
        return 0
    
    strs.sort(key=cmp_to_key(compare))
    
    result = ''.join(strs)
    
    # Handle edge case: all zeros
    return '0' if result[0] == '0' else result
```

## Reasoning
1. **Convert to Strings:** Enables custom concatenation-based comparison
2. **Custom Comparator:** For any two strings a, b, compare a+b vs b+a
3. **Sort with Comparator:** Arranges strings in optimal order for maximum concatenated value
4. **Edge Case Handling:** If result starts with '0', entire array must be zeros (return "0")

## Complexity Analysis
- **Time:** O(n log n · k) where k = average string length (for comparisons)
- **Space:** O(n) for string array

## Edge Cases & Validation
```python
assert largestNumber([10,2]) == "210"
assert largestNumber([3,30,34,5,9]) == "9534330"
assert largestNumber([0,0]) == "0"
assert largestNumber([1]) == "1"
```

## Common Mistakes
1. **Simple Numeric/Lexicographic Sort:** Doesn't produce correct order (e.g., "3" vs "30" needs special comparison)
2. **Missing All-Zeros Edge Case:** Result like "000" instead of "0"
3. **Wrong Comparator Direction:** Confusing which order should return -1 vs 1

## Interview Walkthrough
**Interviewer:** "Arrange numbers to form the largest number."

**Your Response:**
1. "Standard sorting doesn't work—need custom comparator based on concatenation."
2. "For any two numbers a, b, I compare a+b vs b+a as strings to determine order."
3. "After sorting, handle edge case where all numbers are zero."

## Related Problems
- [[Sorting Representative Problems]]

## Related Concepts
- [[Sorting]] — custom comparator technique


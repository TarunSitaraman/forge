---
type: problem
status: stable
tags: [dsa/problem, dsa/two-pointers, dsa/arrays]
canonical: true
related: [[[Two Pointers]], [[Python - Two Pointers]], [[Binary Search]]]
difficulty: easy
---
# Two Pointers - Two Sum Sorted

## Problem Statement
Given a **sorted** array of integers and a target sum, find two numbers that add up to the target. Return their indices.

**Constraints:**
- Array is **sorted** (crucial for this approach)
- Each element can be used only once
- Exactly one solution exists
- Indices are 1-indexed or 0-indexed (varies by problem)

## Classification
- **Patterns:** [[Two Pointers]]
- **Template:** [[Python - Two Pointers]]
- **Category:** Converge from opposite ends for exact target

## Pattern Selection
This is the canonical first application of Two Pointers because:
1. Sorted array enables bidirectional search
2. Converging pointers (left/right) naturally explore candidates
3. Each comparison eliminates half the remaining search space
4. No need for extra data structures like hash maps

## Why Two Pointers Works
**Invariant:** Every unexamined pair remains between left and right pointers.

**Pointer Movement:**
- If `nums[left] + nums[right] < target`: right side is too small, increment left (need larger sum)
- If `nums[left] + nums[right] > target`: left side is too large, decrement right (need smaller sum)
- If `nums[left] + nums[right] == target`: found the answer

Because the array is sorted, this monotonic movement is safe:
- Moving left increases sum (left pointer moves to larger values)
- Moving right decreases sum (right pointer moves to smaller values)
- We never skip a valid pair

## Python Solution
```python
def two_sum(numbers: list[int], target: int) -> list[int]:
    """
    Find two numbers in sorted array that sum to target.
    Time: O(n) single pass
    Space: O(1) no extra data structures
    """
    left, right = 0, len(numbers) - 1
    
    while left < right:
        current_sum = numbers[left] + numbers[right]
        
        if current_sum == target:
            return [left + 1, right + 1]  # 1-indexed
        elif current_sum < target:
            left += 1  # Need larger sum
        else:
            right -= 1  # Need smaller sum
    
    return []  # No solution found
```

## Reasoning
1. **Start:** Pointers at extremes (smallest and largest values)
2. **Comparison:** Evaluate current sum against target
3. **Movement:**
   - Too small: move left pointer (increase sum)
   - Too large: move right pointer (decrease sum)
   - Exact match: return indices
4. **Termination:** Pointers cross (no solution) or solution found

## Complexity Analysis
- **Time:** O(n) — each pointer moves at most n times, single pass
- **Space:** O(1) — only variables for pointers and sum

## Edge Cases & Validation
```python
# Two elements
assert two_sum([2, 7], 9) == [1, 2]
assert two_sum([2, 7], 10) == []

# Duplicates
assert two_sum([1, 2, 3, 6, 9], 15) == [4, 5]  # 6 + 9

# Negative numbers
assert two_sum([-10, -5, 0, 5, 10], 0) == [2, 5]  # -5 + 5

# Large gap between solution
assert two_sum([1, 2, 3, 10, 20, 30], 31) == [4, 6]  # 1 + 30
```

## Common Mistakes
1. **[[Off-by-One]]**: Returning 0-indexed instead of 1-indexed (or vice versa)
2. **[[Boundary Errors]]**: Using `left <= right` instead of `left < right`
3. **[[Infinite Loop]]**: Moving both pointers in same direction or not moving at all
4. **Forgetting Sort:** This solution only works on sorted input

## Interview Walkthrough
**Interviewer:** "Find two numbers summing to target in a sorted array."

**Your Response:**
1. "The array is sorted, so I can use two pointers from opposite ends."
2. "If the sum is too small, I move left pointer right (to get larger values)."
3. "If the sum is too large, I move right pointer left (to get smaller values)."
4. "This guarantees I examine all candidate pairs without duplication."

**Why This Resonates:**
- Shows you recognize structure (sorted array) and exploit it
- Explains pointer movement logic clearly
- Demonstrates single-pass O(n) thinking vs. hash table approach

## Follow-Up Variations
1. **Unsorted Array:** Use hash map instead (O(n) time, O(n) space)
2. **Find All Pairs:** Iterate left, and for each position find matching right pointer
3. **K-Sum:** Reduce to 2-sum recursively; sort first, then nested loops
4. **Return Pair Values:** Instead of indices, return [num1, num2]
5. **3Sum:** Sort, fix one value, then 2-sum on remainder (O(n²) time)

## Interview Prep Notes
- **Common Ask:** "How would this change if the array wasn't sorted?"
- **Variant Pressure:** "Find all pairs" or "minimize absolute difference"
- **Time Complexity:** Contrast with hash map solution (same O(n) but different trade-offs)

## Related Problems
- [[Two Pointers - Container With Most Water]] (similar convergent pointers, different optimization)
- [[Two Pointers - Remove Duplicates]] (same structure, different operation)
- [[Two Pointers Representative Problems]]
- [[Binary Search]] (alternative for finding complement in sorted array)

## Related Concepts
- [[Sorting]] — why sorted input enables this technique
- [[Two Pointers]] — fundamental pattern for convergent searches
- [[Hash Map]] — alternative O(n) approach without sorting requirement


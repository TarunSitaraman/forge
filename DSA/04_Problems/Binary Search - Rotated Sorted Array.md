---
type: problem
status: stable
tags: [dsa/problem, dsa/binary-search]
canonical: true
related: [[[Binary Search]], [[Python - Binary Search]]]
difficulty: medium
---
# Binary Search - Rotated Sorted Array

## Problem Statement
Given a sorted array that has been rotated at an unknown pivot point, and a target value, return the index of target if found, otherwise -1.

**Constraints:**
- Original array is sorted in ascending order
- Array is rotated at unknown pivot (e.g., [4,5,6,7,0,1,2] rotated from [0,1,2,4,5,6,7])
- All elements are distinct
- Must run in O(log n) time

## Classification
- **Patterns:** [[Binary Search]]
- **Template:** [[Python - Binary Search]]
- **Category:** Modified binary search on rotated sorted structure

## Pattern Selection
This is canonical for Binary Search adaptation because:
1. Array isn't fully sorted, but has "sorted halves" property
2. At any point, at least one half (left or right of mid) is properly sorted
3. Can determine which half is sorted, then check if target lies in that range
4. Still achieves O(log n) by eliminating half the search space each iteration

## Why Binary Search Works (Modified)
**Invariant:** At any point, one half of the current range [lo, hi] is properly sorted (no rotation within that half).

**Key Insight:**
- Compare `nums[lo]` with `nums[mid]`:
  - If `nums[lo] <= nums[mid]`: left half is sorted
  - Otherwise: right half is sorted
- Once we know which half is sorted, check if target lies in that sorted range
- If yes, search that half; if no, search the other half

## Python Solution
```python
def search(nums: list[int], target: int) -> int:
    """
    Search for target in rotated sorted array.
    Time: O(log n)
    Space: O(1)
    """
    lo, hi = 0, len(nums) - 1
    
    while lo <= hi:
        mid = lo + (hi - lo) // 2
        
        if nums[mid] == target:
            return mid
        
        # Determine which half is properly sorted
        if nums[lo] <= nums[mid]:
            # Left half [lo, mid] is sorted
            if nums[lo] <= target < nums[mid]:
                hi = mid - 1  # Target in left half
            else:
                lo = mid + 1  # Target in right half
        else:
            # Right half [mid, hi] is sorted
            if nums[mid] < target <= nums[hi]:
                lo = mid + 1  # Target in right half
            else:
                hi = mid - 1  # Target in left half
    
    return -1  # Target not found


def findMin(nums: list[int]) -> int:
    """
    Find minimum element in rotated sorted array (pivot point).
    Related problem using same rotation insight.
    """
    lo, hi = 0, len(nums) - 1
    
    while lo < hi:
        mid = lo + (hi - lo) // 2
        
        if nums[mid] > nums[hi]:
            # Minimum is in right half
            lo = mid + 1
        else:
            # Minimum is in left half (including mid)
            hi = mid
    
    return nums[lo]
```

## Reasoning
1. **Standard Binary Search Setup:** lo, hi pointers, calculate mid
2. **Direct Match Check:** If nums[mid] equals target, return immediately
3. **Identify Sorted Half:** Compare nums[lo] with nums[mid]
   - If nums[lo] <= nums[mid], left half is sorted (no rotation point within)
   - Otherwise, right half must be sorted
4. **Check Target Range:** Determine if target lies within the sorted half's range
5. **Adjust Search Space:** Move to appropriate half based on where target could be
6. **Termination:** Target found or search space exhausted (-1)

## Complexity Analysis
- **Time:** O(log n)
  - Each iteration eliminates half the search space
  - Same guarantee as standard binary search despite added complexity

- **Space:** O(1)
  - Only using pointer variables, no extra data structures

## Edge Cases & Validation
```python
# No rotation (already sorted)
assert search([1,2,3,4,5], 3) == 2

# Rotated with target in left sorted half
assert search([4,5,6,7,0,1,2], 5) == 1

# Rotated with target in right sorted half  
assert search([4,5,6,7,0,1,2], 1) == 5

# Target not present
assert search([4,5,6,7,0,1,2], 3) == -1

# Single element
assert search([1], 1) == 0
assert search([1], 0) == -1

# Two elements, rotated
assert search([3,1], 1) == 1

# Rotation at start (no actual rotation)
assert search([1,2,3,4,5], 1) == 0

# Rotation point search
assert findMin([3,4,5,1,2]) == 1
assert findMin([4,5,6,7,0,1,2]) == 0
```

## Common Mistakes
1. **[[Boundary Errors]]:** Incorrect comparison for sorted half detection
   - Must use `<=` not `<` when comparing nums[lo] and nums[mid]
   - Handles edge case where lo == mid (single element remaining)

2. **[[Wrong Mid Calculation]]:** Range check boundaries
   - `nums[lo] <= target < nums[mid]` for left half (inclusive lo, exclusive mid)
   - Getting these boundary conditions wrong causes incorrect half selection

3. **Confusing Sorted Half Direction:**
   - Easy to mix up which half is sorted
   - Always verify: if nums[lo] <= nums[mid], LEFT is sorted

4. **Not Handling Duplicates:**
   - This solution assumes distinct elements
   - With duplicates, nums[lo] == nums[mid] == nums[hi] breaks the logic (need linear fallback)

## Interview Walkthrough
**Interviewer:** "Search in a rotated sorted array in O(log n)."

**Your Response:**
1. "Even though the array isn't fully sorted, one half is always properly sorted."
2. "I compare nums[lo] with nums[mid] to determine which half is sorted."
3. "If target falls within that sorted half's range, I search there; otherwise the other half."
4. "This maintains O(log n) by still eliminating half the search space each time."

**Diagram It:**
```
Array: [4, 5, 6, 7, 0, 1, 2], target = 0

lo=0, hi=6, mid=3: nums[mid]=7
nums[lo]=4 <= nums[mid]=7 → LEFT half [4,5,6,7] is sorted
Is target(0) in [4, 7)? No → search RIGHT half
lo = mid+1 = 4

lo=4, hi=6, mid=5: nums[mid]=1
nums[lo]=0 <= nums[mid]=1 → LEFT half [0,1] is sorted  
Is target(0) in [0, 1)? Yes → search LEFT half
hi = mid-1 = 4

lo=4, hi=4, mid=4: nums[mid]=0 == target! Return 4
```

**Why This Resonates:**
- Shows deep understanding of WHY one half is always sorted
- Explains the decision tree clearly with concrete example
- Demonstrates O(log n) guarantee despite modified logic

## Variations
1. **Find Minimum (Pivot Point):** Simpler version, just find rotation point
2. **Search with Duplicates:** Requires fallback to linear scan when ambiguous
3. **Find Maximum:** Similar to minimum but opposite comparison
4. **Count Rotations:** Same as finding pivot index

## Related Problems
- [[Binary Search - First Bad Version]] (different binary search application)
- [[Binary Search Representative Problems]]

## Related Concepts
- [[Binary Search]] — foundational pattern being adapted
- [[Sorted Array]] — property being exploited
- [[Pivot Point]] — rotation point concept


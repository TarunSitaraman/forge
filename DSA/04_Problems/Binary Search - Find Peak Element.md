---
type: problem
status: stable
tags: [dsa/problem, dsa/binary-search]
canonical: true
related: [[[Binary Search]], [[Python - Binary Search]]]
difficulty: medium
---
# Binary Search - Find Peak Element

## Problem Statement
A peak element is an element strictly greater than its neighbors. Given an integer array `nums`, find a peak element and return its index. If the array has multiple peaks, return the index to any of them. Assume `nums[-1] = nums[n] = -∞`.

**Constraints:**
- 1 ≤ nums.length ≤ 1000
- Adjacent elements are never equal
- Must run in O(log n) time

## Classification
- **Patterns:** [[Binary Search]]
- **Template:** [[Python - Binary Search]]
- **Category:** Binary search on unsorted array using local comparison

## Pattern Selection
This is canonical for Binary Search adaptation because:
1. Array is NOT sorted, yet binary search still applies
2. Key insight: comparing adjacent elements reveals which direction has a peak
3. If `nums[mid] < nums[mid+1]`, a peak must exist to the right
4. If `nums[mid] > nums[mid+1]`, a peak exists at mid or to the left

## Why Binary Search Works (Despite Unsorted Array)
**Invariant:** If we're ascending (nums[mid] < nums[mid+1]), a peak MUST exist somewhere to the right (since sequence must eventually decrease or hit boundary).

**Proof of Correctness:**
- Boundary conditions treat out-of-bounds as -∞
- If ascending at mid, moving right guarantees finding higher values until a peak
- If descending at mid, the peak is at mid or somewhere to the left
- This local comparison always leads toward SOME peak (not necessarily global maximum)

## Python Solution
```python
def findPeakElement(nums: list[int]) -> int:
    """
    Find any peak element index using binary search.
    Time: O(log n)
    Space: O(1)
    """
    lo, hi = 0, len(nums) - 1
    
    while lo < hi:
        mid = lo + (hi - lo) // 2
        
        if nums[mid] < nums[mid + 1]:
            # Ascending: peak must be to the right
            lo = mid + 1
        else:
            # Descending (or peak found): peak is at mid or to the left
            hi = mid
    
    return lo  # lo == hi, pointing to a peak
```

## Reasoning
1. **Boundary Setup:** lo=0, hi=n-1 (search entire array)
2. **Compare Adjacent:** At each mid, compare nums[mid] with nums[mid+1]
3. **Ascending Case:** If nums[mid] < nums[mid+1], we're on an upward slope
   - Peak must exist further right (sequence eventually turns down or hits boundary)
   - Move lo to mid+1
4. **Descending Case:** If nums[mid] >= nums[mid+1], we're at or past a peak
   - Peak is at mid or somewhere to the left
   - Move hi to mid (not mid-1, since mid itself could be the peak)
5. **Convergence:** When lo == hi, this index is guaranteed to be a peak

## Complexity Analysis
- **Time:** O(log n) — standard binary search elimination
- **Space:** O(1) — only pointer variables

## Edge Cases & Validation
```python
# Single element (trivially a peak)
assert findPeakElement([1]) == 0

# Two elements, ascending
result = findPeakElement([1, 2])
assert result == 1  # nums[1]=2 is peak

# Two elements, descending
result = findPeakElement([2, 1])
assert result == 0  # nums[0]=2 is peak

# Peak in middle
nums = [1, 2, 3, 1]
result = findPeakElement(nums)
assert nums[result] == 3

# Multiple peaks (any valid answer)
nums = [1, 2, 1, 3, 5, 6, 4]
result = findPeakElement(nums)
assert result in [1, 5]  # Both are valid peaks

# Strictly increasing (peak at end)
nums = [1, 2, 3, 4, 5]
result = findPeakElement(nums)
assert result == 4

# Strictly decreasing (peak at start)
nums = [5, 4, 3, 2, 1]
result = findPeakElement(nums)
assert result == 0
```

## Common Mistakes
1. **[[Boundary Errors]]:** Using `hi = mid - 1` in descending case
   - Wrong: might skip over mid itself if it's the actual peak
   - Correct: `hi = mid` since mid could BE the peak

2. **Confusing "Any Peak" with "Maximum Peak":**
   - Problem asks for ANY peak, not necessarily global maximum
   - Multiple valid answers are acceptable

3. **Not Handling Array Boundaries as -∞:**
   - Must conceptually treat out-of-bounds neighbors as -∞
   - This ensures algorithm always terminates at a valid peak

4. **Attempting Linear Comparison Instead of Binary Search:**
   - Missing the key insight that local slope indicates direction to peak
   - This IS a valid binary search application despite unsorted data

## Interview Walkthrough
**Interviewer:** "Find any peak element in O(log n) time. Array isn't sorted."

**Your Response:**
1. "Even though array isn't sorted, I can still binary search using LOCAL comparison."
2. "If nums[mid] < nums[mid+1], I'm on an ascending slope—a peak must exist further right."
3. "If nums[mid] >= nums[mid+1], the peak is at mid or somewhere to the left."
4. "This guarantees convergence to SOME peak in O(log n), even though many peaks may exist."

**Why This Resonates:**
- Shows understanding that binary search doesn't require GLOBAL sorted order
- Explains the local slope insight that makes this work
- Demonstrates correct boundary handling (`hi = mid`, not `mid - 1`)

## Variations
1. **Find Peak in 2D Matrix:** Extend to 2D grid with similar local comparison
2. **Find All Peaks:** Would require O(n) linear scan (can't binary search for ALL peaks)
3. **Peak in Mountain Array:** Similar problem but guarantees single peak (simpler)
4. **Local Minimum:** Same technique, opposite comparison direction

## Related Problems
- [[Binary Search - Rotated Sorted Array]] (different unsorted binary search adaptation)
- [[Binary Search Representative Problems]]

## Related Concepts
- [[Binary Search]] — core pattern being creatively applied
- [[Local Extrema]] — mathematical concept being exploited
- [[Monotonic Sequences]] — understanding slopes for direction


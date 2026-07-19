---
type: problem
status: stable
tags: [dsa/problem, dsa/interval-processing]
canonical: true
related: [[[Interval Processing]]]
difficulty: medium
---
# Interval Processing - Insert Interval

## Problem Statement
Given a set of non-overlapping intervals sorted by start time, insert a new interval and merge if necessary to maintain non-overlapping, sorted order.

**Constraints:**
- Input intervals are already sorted and non-overlapping
- New interval may overlap with zero, one, or multiple existing intervals

## Classification
- **Patterns:** [[Interval Processing]]
- **Category:** Three-phase interval insertion with merging

## Pattern Selection
This is canonical for Interval Processing because it demonstrates a clean three-phase approach: intervals entirely before the new one, overlapping intervals (merge these), and intervals entirely after.

## Why This Approach Works
**Key Insight:** Since input is already sorted, we can process linearly: 
1. Add all intervals ending before new interval starts (no overlap)
2. Merge all intervals that overlap with new interval into one combined interval
3. Add all remaining intervals (starting after new interval ends)

## Python Solution
```python
def insert(intervals: list[list[int]], newInterval: list[int]) -> list[list[int]]:
    """
    Insert new interval into sorted non-overlapping intervals, merging as needed.
    Time: O(n) single pass
    Space: O(n) for result
    """
    result = []
    i = 0
    n = len(intervals)
    
    # Phase 1: Add all intervals ending before newInterval starts
    while i < n and intervals[i][1] < newInterval[0]:
        result.append(intervals[i])
        i += 1
    
    # Phase 2: Merge all overlapping intervals with newInterval
    while i < n and intervals[i][0] <= newInterval[1]:
        newInterval[0] = min(newInterval[0], intervals[i][0])
        newInterval[1] = max(newInterval[1], intervals[i][1])
        i += 1
    result.append(newInterval)
    
    # Phase 3: Add remaining intervals
    while i < n:
        result.append(intervals[i])
        i += 1
    
    return result
```

## Reasoning
1. **Phase 1 (Before):** While current interval ends strictly before new interval starts, no overlap possible—add directly
2. **Phase 2 (Overlap/Merge):** While current interval starts before or at new interval's end, they overlap—expand new interval's bounds to encompass both
3. **Phase 3 (After):** Remaining intervals start after merged interval ends—add directly
4. **Single Pass:** Each interval examined exactly once across all three phases

## Complexity Analysis
- **Time:** O(n) — single linear pass through intervals
- **Space:** O(n) — result array

## Edge Cases & Validation
```python
assert insert([[1,3],[6,9]], [2,5]) == [[1,5],[6,9]]
assert insert([[1,2],[3,5],[6,7],[8,10],[12,16]], [4,8]) == [[1,2],[3,10],[12,16]]
assert insert([], [5,7]) == [[5,7]]
assert insert([[1,5]], [2,3]) == [[1,5]]  # New interval fully contained
assert insert([[1,5]], [6,8]) == [[1,5],[6,8]]  # No overlap
```

## Common Mistakes
1. **Wrong Overlap Condition:** Using `<` instead of `<=` in Phase 1/2 boundary checks (touching intervals should merge)
2. **Not Updating newInterval Bounds Correctly:** Must take min of starts and max of ends across ALL overlapping intervals, not just first one
3. **Missing Phase 3:** Forgetting to add remaining intervals after the merge phase completes

## Interview Walkthrough
**Interviewer:** "Insert new interval into sorted list, merging overlaps."

**Your Response:**
1. "Since intervals are pre-sorted, I can process in three linear phases."
2. "First, add all intervals ending before the new one starts—no overlap possible."
3. "Then, merge all overlapping intervals by expanding the new interval's bounds."
4. "Finally, add all remaining intervals that start after the merged result ends."
5. "This achieves O(n) with a single pass, exploiting the sorted input property."

## Related Problems
- [[Interval Processing - Merge Intervals]] (related merging technique, different starting condition)
- [[Interval Processing Representative Problems]]

## Related Concepts
- [[Interval Processing]] — three-phase insertion pattern


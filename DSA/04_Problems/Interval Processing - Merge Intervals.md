---
type: problem
status: stable
tags: [dsa/problem, dsa/interval-processing]
canonical: true
related: [[[Interval Processing]], [[Greedy]]]
difficulty: medium
---
# Interval Processing - Merge Intervals

## Problem Statement
Given an array of intervals, merge all overlapping intervals and return non-overlapping intervals that cover all input intervals.

**Constraints:**
- Intervals given as [start, end] pairs
- 1 ≤ intervals.length ≤ 10^4

## Classification
- **Patterns:** [[Interval Processing]]
- **Category:** Sort-then-merge interval consolidation

## Pattern Selection
This is canonical for Interval Processing because sorting by start time enables single-pass merging of overlapping ranges.

## Why This Approach Works
**Key Insight:** After sorting by start time, any overlap must be with the immediately preceding interval in the result (since starts are processed in order).

## Python Solution
```python
def merge(intervals: list[list[int]]) -> list[list[int]]:
    """
    Merge overlapping intervals after sorting.
    Time: O(n log n) for sorting
    Space: O(n) for result
    """
    if not intervals:
        return []
    
    intervals.sort(key=lambda x: x[0])  # Sort by start time
    result = [intervals[0]]
    
    for start, end in intervals[1:]:
        last_end = result[-1][1]
        
        if start <= last_end:
            # Overlap: merge by extending end if needed
            result[-1][1] = max(last_end, end)
        else:
            # No overlap: add as new interval
            result.append([start, end])
    
    return result
```

## Reasoning
1. **Sort by Start:** Ensures we process intervals in left-to-right order
2. **Initialize Result:** Start with first interval
3. **Check Overlap:** Current start <= previous end means overlap
4. **Merge or Add:** Extend previous end (taking max) if overlap, else add new interval

## Complexity Analysis
- **Time:** O(n log n) — dominated by sorting
- **Space:** O(n) — result array (or O(log n) if not counting output)

## Edge Cases & Validation
```python
assert merge([[1,3],[2,6],[8,10],[15,18]]) == [[1,6],[8,10],[15,18]]
assert merge([[1,4],[4,5]]) == [[1,5]]  # Touching intervals merge
assert merge([[1,4]]) == [[1,4]]  # Single interval
assert merge([]) == []
```

## Common Mistakes
1. **Sorting by End Instead of Start:** Breaks merging logic (need start-order for this problem)
2. **Not Taking Max for End:** `result[-1][1] = end` instead of `max(last_end, end)` misses nested intervals
3. **Off-by-one in Overlap Check:** `start < last_end` misses touching intervals (should be `<=`)

## Interview Walkthrough
**Interviewer:** "Merge overlapping intervals."

**Your Response:**
1. "I'll sort by start time first—this ensures sequential processing."
2. "For each interval, check if it overlaps with the last merged interval."
3. "If it does, extend the end (taking max, since current might be nested inside previous)."

## Related Problems
- [[Interval Processing Representative Problems]]
- [[Greedy - Interval Scheduling]] (different goal: remove vs merge)

## Related Concepts
- [[Interval Processing]] — core pattern
- [[Greedy]] — related sorting-based approach


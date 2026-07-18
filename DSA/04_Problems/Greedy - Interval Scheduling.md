---
type: problem
status: stable
tags: [dsa/problem, dsa/greedy, dsa/intervals]
canonical: true
related: [[[Greedy]], [[Interval Processing]]]
difficulty: medium
---
# Greedy - Interval Scheduling (Non-overlapping Intervals)

## Problem Statement
Given a collection of intervals, find the minimum number of intervals to remove to make the rest non-overlapping.

**Constraints:**
- Intervals given as [start, end] pairs
- Intervals may partially overlap
- 1 ≤ intervals.length ≤ 10^5

## Classification
- **Patterns:** [[Greedy]], [[Interval Processing]]
- **Category:** Interval scheduling maximization via sorting

## Pattern Selection
This is canonical for Greedy because:
1. Sort by END time (not start time) - crucial insight
2. Greedily select intervals with earliest end time
3. Provable optimal: earliest-ending interval leaves maximum room for future selections
4. Equivalent to: maximize non-overlapping intervals, then subtract from total

## Why Greedy Works (Proof Sketch)
**Exchange Argument:** Suppose optimal solution doesn't include the earliest-ending interval. We can always swap it in without making the solution worse (it ends earlier or same, leaving equal or more room for subsequent intervals).

**Key Insight:** Sorting by END time (not start) is crucial. Sorting by start time doesn't provide the same guarantee.

## Python Solution
```python
def eraseOverlapIntervals(intervals: list[list[int]]) -> int:
    """
    Find minimum intervals to remove for non-overlapping result.
    Time: O(n log n) for sorting
    Space: O(1) excluding sort space
    """
    if not intervals:
        return 0
    
    # Sort by END time - the crucial greedy choice
    intervals.sort(key=lambda x: x[1])
    
    count = 0  # Intervals to remove
    prev_end = intervals[0][1]
    
    for start, end in intervals[1:]:
        if start < prev_end:
            # Overlap detected: remove this interval (greedy choice)
            count += 1
        else:
            # No overlap: update prev_end, keep this interval
            prev_end = end
    
    return count


def maxNonOverlapping(intervals: list[list[int]]) -> int:
    """
    Related: find MAXIMUM number of non-overlapping intervals (not minimum removals).
    Same greedy strategy, different return value.
    """
    if not intervals:
        return 0
    
    intervals.sort(key=lambda x: x[1])
    
    count = 1  # First interval always selected
    prev_end = intervals[0][1]
    
    for start, end in intervals[1:]:
        if start >= prev_end:
            count += 1
            prev_end = end
    
    return count
```

## Reasoning
1. **Sort by End Time:** This is THE critical greedy insight—not sorting by start time
2. **Track Previous End:** Maintain the end time of last selected (non-removed) interval
3. **Overlap Check:** If current start < previous end, overlap exists—remove current interval
4. **No Overlap:** Update previous end to current interval's end, continue
5. **Count Removals:** Track how many intervals were removed due to overlap

## Complexity Analysis
- **Time:** O(n log n) — dominated by sorting
- **Space:** O(1) — excluding sort's internal space, or O(log n) for sort's recursion

## Edge Cases & Validation
```python
# Simple overlap
assert eraseOverlapIntervals([[1,2],[2,3],[3,4],[1,3]]) == 1

# No overlaps
assert eraseOverlapIntervals([[1,2],[2,3],[3,4]]) == 0

# All overlapping
assert eraseOverlapIntervals([[1,2],[1,2],[1,2]]) == 2

# Single interval
assert eraseOverlapIntervals([[1,2]]) == 0

# Empty input
assert eraseOverlapIntervals([]) == 0

# Complex overlapping pattern
assert eraseOverlapIntervals([[1,100],[11,22],[1,11],[2,12]]) == 2
```

## Common Mistakes
1. **Sorting by Start Instead of End:** This breaks the greedy guarantee entirely
   - Wrong: `intervals.sort(key=lambda x: x[0])`
   - Correct: `intervals.sort(key=lambda x: x[1])`

2. **Boundary Confusion:** Should overlap check be `<` or `<=`?
   - Depends on whether touching intervals (end == start) count as overlapping
   - Standard: `start < prev_end` (touching is OK, strict overlap only)

3. **Not Proving Why Sort-by-End Works:** 
   - Must be able to explain the exchange argument if asked

4. **Off-by-One in Counting:** Confusing "intervals to keep" vs. "intervals to remove"

## Interview Walkthrough
**Interviewer:** "Find minimum intervals to remove for non-overlapping result."

**Your Response:**
1. "I'll sort intervals by their END time—this is the crucial insight, not start time."
2. "Greedily, I keep the earliest-ending interval and remove any that overlap with it."
3. "This works because the earliest-ending interval leaves maximum room for future intervals."
4. "Provable via exchange argument: any optimal solution can be modified to include this choice without losing optimality."

**Why This Resonates:**
- Explicitly identifies the counter-intuitive but crucial sort-by-end insight
- Provides proof sketch (exchange argument) showing deep understanding
- Distinguishes from naive sort-by-start approach that would fail

## Variations
1. **Maximum Non-overlapping Intervals:** Same algorithm, different return value (count kept, not removed)
2. **Minimum Meeting Rooms:** Different problem - count simultaneous overlaps (needs different approach)
3. **Merge Intervals:** Different goal - combine overlapping intervals rather than remove them

## Related Problems
- [[Greedy Representative Problems]]
- [[Interval Processing Representative Problems]]

## Related Concepts
- [[Greedy]] — exchange argument proof technique
- [[Interval Processing]] — broader interval manipulation context
- [[Sorting]] — critical choice of sort key


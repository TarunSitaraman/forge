---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/interval-processing]
canonical: true
related: [[[Interval Processing]], [[Coding Interviews]]]
---
# Interval Processing Interview Guide

## Pattern Recognition
**Green Flags:** `merge intervals`, `meeting rooms`, overlapping ranges, scheduling conflicts

## Communication Template
`I'll sort intervals by start time, then merge consecutive intervals that overlap (current start <= previous end).`

## Core Mechanics
```python
def merge_intervals(intervals):
    if not intervals:
        return []
    intervals.sort(key=lambda x: x[0])
    result = [intervals[0]]
    for start, end in intervals[1:]:
        if start <= result[-1][1]:
            result[-1][1] = max(result[-1][1], end)
        else:
            result.append([start, end])
    return result
```

## Common Mistakes
1. Wrong sort key (sorting by end instead of start for merging)
2. Off-by-one in overlap check (<=vs<)
3. Not updating end to max when merging (partial overlap missed)

## Practice Problems
- Merge Intervals, Meeting Rooms II, Insert Interval

## Checklist
- [ ] Sorted by correct key for the operation
- [ ] Overlap condition correctly uses <= or < based on inclusivity
- [ ] Verified merge takes max of ends, not just latest

## Related
- [[Interval Processing]]
- [[Interval Processing Representative Problems]]

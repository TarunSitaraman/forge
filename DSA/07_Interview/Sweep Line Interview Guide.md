---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/sweep-line]
canonical: true
related: [[[Sweep Line]], [[Coding Interviews]]]
---
# Sweep Line Interview Guide

## Pattern Recognition
**Green Flags:** Events with start/end times; skyline problems; interval coverage; scheduling conflicts over time

## Communication Template
`I'll convert intervals into events (start: +1, end: -1), sort by time, then sweep through tracking cumulative active count.`

## Core Mechanics
```python
def min_meeting_rooms(intervals):
    events = []
    for start, end in intervals:
        events.append((start, 1))   # Room needed
        events.append((end, -1))    # Room freed
    events.sort()
    
    rooms = max_rooms = 0
    for time, delta in events:
        rooms += delta
        max_rooms = max(max_rooms, rooms)
    return max_rooms
```

## Common Mistakes
1. Wrong event ordering when start == end at same time (process end before start typically)
2. Not sorting events before sweeping
3. Confusing +1/-1 semantics (start increases active count, end decreases)

## Practice Problems
- Meeting Rooms II, Skyline Problem, Employee Free Time

## Checklist
- [ ] Events correctly generated from intervals
- [ ] Tie-breaking rule established for same-time events
- [ ] O(n log n) complexity confirmed (sort dominates)

## Related
- [[Sweep Line]]
- [[Sweep Line Representative Problems]]

---
type: problem
status: stable
tags: [dsa/problem, dsa/sweep-line]
canonical: true
related: [[[Sweep Line]], [[Interval Processing]]]
difficulty: medium
---
# Sweep Line - Meeting Rooms II

## Problem Statement
Given meeting time intervals, find the minimum number of conference rooms required to hold all meetings.

**Constraints:**
- Intervals given as [start, end] pairs
- Meetings may overlap requiring multiple simultaneous rooms

## Classification
- **Patterns:** [[Sweep Line]]
- **Category:** Event-based maximum overlap counting

## Pattern Selection
This is canonical for Sweep Line because we need to track maximum simultaneous overlaps, which is naturally solved by converting intervals to events and sweeping through time.

## Why Sweep Line Works
**Key Insight:** Convert each interval into two events: (start, +1 room needed) and (end, -1 room freed). Sort events by time, sweep through tracking running count—the maximum count is the answer.

## Python Solution
```python
def minMeetingRooms(intervals: list[list[int]]) -> int:
    """
    Find minimum rooms needed using sweep line technique.
    Time: O(n log n) for sorting
    Space: O(n) for events
    """
    if not intervals:
        return 0
    
    events = []
    for start, end in intervals:
        events.append((start, 1))   # Room needed
        events.append((end, -1))    # Room freed
    
    # Sort by time; if tie, process end (-1) before start (+1)
    # This handles back-to-back meetings correctly
    events.sort(key=lambda x: (x[0], x[1]))
    
    rooms = 0
    max_rooms = 0
    
    for time, delta in events:
        rooms += delta
        max_rooms = max(max_rooms, rooms)
    
    return max_rooms
```

## Reasoning
1. **Convert to Events:** Each interval becomes two events (start=+1, end=-1)
2. **Sort by Time:** Process events chronologically; ties broken by processing end before start
3. **Sweep and Track:** Running sum represents current rooms in use
4. **Track Maximum:** Peak value during sweep is the answer

## Complexity Analysis
- **Time:** O(n log n) — dominated by sorting 2n events
- **Space:** O(n) — events array

## Edge Cases & Validation
```python
assert minMeetingRooms([[0,30],[5,10],[15,20]]) == 2
assert minMeetingRooms([[7,10],[2,4]]) == 1
assert minMeetingRooms([[1,5],[8,9],[8,9]]) == 2  # Two meetings same time
assert minMeetingRooms([]) == 0
```

## Common Mistakes
1. **Wrong Tie-Breaking:** If meeting ends at time 5 and another starts at 5, processing start first wrongly suggests 2 rooms needed when 1 suffices
2. **Using Heap Instead (More Complex):** Alternative approach works but sweep line is simpler here

## Interview Walkthrough
**Interviewer:** "Find minimum meeting rooms needed."

**Your Response:**
1. "I'll convert each interval into two events: room needed at start, room freed at end."
2. "Sort events by time, sweep through tracking running room count."
3. "Maximum count during sweep is the answer—that's the peak simultaneous overlap."
4. "Critical tie-breaking: process 'end' events before 'start' events at same timestamp."

## Related Problems
- [[Sweep Line Representative Problems]]
- [[Interval Processing - Merge Intervals]] (related interval problem, different goal)

## Related Concepts
- [[Sweep Line]] — event-based processing
- [[Interval Processing]] — broader interval context


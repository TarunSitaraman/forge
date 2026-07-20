---
type: problem
status: stable
tags: [dsa/problem, dsa/sweep-line, dsa/heap]
canonical: true
related: [[[Sweep Line]], [[Heap]]]
difficulty: hard
---
# Sweep Line - The Skyline Problem

## Problem Statement
Given a list of buildings `[left, right, height]` (each a rectangle from
`left` to `right` on the x-axis, standing `height` tall), compute the
skyline — the outline formed by all buildings viewed from a distance, as
a list of `[x, height]` key points where the height changes.

**Constraints:**
- Buildings can overlap arbitrarily
- Output must list only points where height actually changes (no
  redundant consecutive points, no two consecutive points at the same
  height)

## Classification
- **Patterns:** [[Sweep Line]], [[Heap]]
- **Category:** Event-driven sweep with a max-heap of active heights

## Pattern Selection
This is the canonical **hard-difficulty** Sweep Line problem, extending
the "count active intervals" pattern (as in
[[Sweep Line - Meeting Rooms II]]) to "track the *maximum* active value"
— which requires a max-heap instead of a simple counter, plus careful
handling of lazy deletion for buildings that have ended.

## Why Sweep Line + Heap Works
**Events:** each building generates a "start" event (at `left`, height
becomes a candidate) and an "end" event (at `right`, height should no
longer count).

**Max-heap of active heights:** at each x-coordinate, the skyline height
is the maximum height among all currently active buildings. A max-heap
gives fast access to that maximum — but heaps don't support efficient
arbitrary removal, so ended buildings are removed **lazily**: left in the
heap, but skipped (popped and discarded) once they surface at the top
after their end x-coordinate has passed.

**Recording a key point:** whenever the current max height differs from
the previous max height, that x-coordinate is a skyline key point.

## Python Solution
```python
import heapq

def getSkyline(buildings: list[list[int]]) -> list[list[int]]:
    """
    Compute the skyline using a sweep line with a lazy-deletion max-heap.
    Time: O(n log n)
    Space: O(n)
    """
    # Events: (x, -height, right) for starts (negative height sorts a
    # taller building first at the same x); (x, 0, None) for ends
    events = []
    for left, right, height in buildings:
        events.append((left, -height, right))
        events.append((right, 0, None))
    events.sort()

    result = []
    live = [(0, float('inf'))]  # (negative-height placeholder, end) — ground level
    heap = [(0, float('inf'))]  # max-heap via negated heights: (-height, end)

    for x, neg_h, right in events:
        if neg_h != 0:
            heapq.heappush(heap, (neg_h, right))
        else:
            pass  # end events don't push; expiration handled by lazy pop below

        # Lazy deletion: discard heap entries whose building has ended by x
        while heap[0][1] <= x:
            heapq.heappop(heap)

        current_height = -heap[0][0]
        if not result or result[-1][1] != current_height:
            result.append([x, current_height])

    return result
```

## Reasoning
1. **Generate events:** a start event carries `(-height, right)` so the
   heap naturally orders tallest-first; an end event carries no useful
   heap payload — it just triggers a re-check.
2. **Sort events by x**, with ties broken so a taller start is processed
   before a shorter one at the same x (via the negative-height encoding).
3. **Sweep:** push new starts; lazily discard heap entries whose
   building has already ended by the current x.
4. **Record key points:** whenever the current max height changes from
   the last recorded height, append a new `[x, height]` point.

## Complexity Analysis
- **Time:** O(n log n) — sorting 2n events, each heap push/pop O(log n)
- **Space:** O(n) — events list and heap

## Edge Cases & Validation
```python
buildings = [[2,9,10],[3,7,15],[5,12,12],[15,20,10],[19,24,8]]
result = getSkyline(buildings)
expected = [[2,10],[3,15],[7,12],[12,0],[15,10],[20,8],[24,0]]
assert result == expected

# Single building
assert getSkyline([[0,2,3]]) == [[0,3],[2,0]]

# No buildings
assert getSkyline([]) == []
```

## Common Mistakes
1. **Not lazily removing ended buildings before reading the max:**
   without the `while heap[0][1] <= x` check, the heap can report a
   height for a building that has already ended — heaps don't support
   efficient direct removal, so this deferred check is required.
2. **Wrong tie-breaking at equal x-coordinates:** if a building starts
   exactly where another ends, or two buildings start at the same x,
   processing order matters — encoding start height as negative (so
   Python's tuple sort naturally orders tallest-starts first) handles
   the common tricky cases.
3. **Recording every event as a key point:** must only emit a point when
   the *effective max height* actually changes — many events don't
   change the visible skyline at all (e.g., a shorter building starting
   underneath a taller one already active).
4. **Forgetting the ground-level sentinel:** without a permanent
   `(0, infinity)` entry in the heap, the heap could empty out and crash
   on `heap[0]` once all real buildings have ended.

## Interview Walkthrough
**Interviewer:** "Compute the skyline outline from these building
rectangles."

**Your Response:**
1. "I'll sweep left to right, tracking the maximum active building
   height at each x — that maximum, when it changes, is exactly a
   skyline key point."
2. "A max-heap gives fast access to the current tallest active
   building. I encode start events as negative heights so the heap
   naturally orders tallest-first."
3. "Since heaps don't support efficient arbitrary removal, I handle
   building expirations lazily — pop stale (already-ended) entries off
   the top only when they'd otherwise be read as the current max."
4. "I only emit an output point when the max height actually changes
   from the previous point, avoiding redundant flat segments."

## Variations
1. **Meeting Rooms II:** the simpler sibling — tracks *count* of active
   intervals via a min-heap of end times, not *maximum value* via a
   max-heap ([[Sweep Line - Meeting Rooms II]])
2. **Rectangle Area / Union of Rectangles:** related sweep-line-plus-
   heap-or-segment-tree family, but tracking covered area instead of an
   outline

## Related Problems
- [[Sweep Line - Meeting Rooms II]] (foundational event-sweep mechanics
  this problem extends with a max-heap)
- [[Sweep Line Representative Problems]]

## Related Concepts
- [[Sweep Line]] — event-ordered processing
- [[Heap]] — max-height tracking with lazy deletion


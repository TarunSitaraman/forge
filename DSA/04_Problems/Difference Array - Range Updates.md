---
type: problem
status: stable
tags: [dsa/problem, dsa/difference-array]
canonical: true
related: [[[Difference Array]], [[Prefix Sum]]]
difficulty: medium
---
# Difference Array - Range Updates (Car Pooling)

## Problem Statement
Given trips [numPassengers, start, end] for a car with given capacity, determine if all trips can be fulfilled without exceeding capacity at any point.

**Constraints:**
- Multiple trips may overlap
- Capacity is fixed maximum passengers

## Classification
- **Patterns:** [[Difference Array]]
- **Category:** Range update with capacity validation

## Pattern Selection
This is canonical for Difference Array because multiple range updates (passenger changes over trip duration) need efficient O(1) per-update processing before final validation.

## Why Difference Array Works
**Key Insight:** Instead of updating every point in range [start, end) for each trip (O(n) per trip), mark only the boundaries: diff[start] += passengers, diff[end] -= passengers. Final prefix sum gives actual passenger count at each point.

## Python Solution
```python
def carPooling(trips: list[list[int]], capacity: int) -> bool:
    """
    Check if all trips can be fulfilled within capacity.
    Time: O(n + max_location)
    Space: O(max_location)
    """
    max_location = max(trip[2] for trip in trips)
    diff = [0] * (max_location + 1)
    
    for passengers, start, end in trips:
        diff[start] += passengers
        diff[end] -= passengers
    
    current_passengers = 0
    for change in diff:
        current_passengers += change
        if current_passengers > capacity:
            return False
    
    return True
```

## Reasoning
1. **Mark Boundaries:** For each trip, mark passenger increase at start, decrease at end
2. **Compute Prefix Sum:** Running sum of diff array gives actual passengers at each point
3. **Validate Capacity:** Check if running sum ever exceeds capacity

## Complexity Analysis
- **Time:** O(n + max_location) — n trips to mark, then single pass through difference array
- **Space:** O(max_location) — difference array size

## Edge Cases & Validation
```python
assert carPooling([[2,1,5],[3,3,7]], 4) == False
assert carPooling([[2,1,5],[3,3,7]], 5) == True
assert carPooling([[2,1,5]], 3) == True
```

## Common Mistakes
1. **Updating Every Point Instead of Boundaries:** O(n*trip_length) instead of O(n)
2. **Off-by-One at End Point:** Passenger should decrease AT end (drop-off point), not end-1
3. **Not Computing Final Prefix Sum:** Just having difference array isn't enough; must accumulate

## Interview Walkthrough
**Interviewer:** "Validate if car pooling trips stay within capacity."

**Your Response:**
1. "Instead of updating every point in each trip's range, I'll use difference array."
2. "Mark passenger increase at start, decrease at end—O(1) per trip."
3. "Final prefix sum of difference array gives actual passenger count at each point in time."
4. "Check if this ever exceeds capacity."

## Related Problems
- [[Difference Array Representative Problems]]

## Related Concepts
- [[Difference Array]] — core technique
- [[Prefix Sum]] — inverse relationship


---
type: problem
status: stable
tags: [dsa/problem, dsa/divide-and-conquer]
canonical: true
related: [[[Divide and Conquer]]]
difficulty: hard
---
# Divide and Conquer - Closest Pair of Points

## Problem Statement
Given n points in 2D plane, find the minimum distance between any two points.

**Constraints:**
- Naive O(n²) approach must be beaten
- Points given as (x, y) coordinate pairs

## Classification
- **Patterns:** [[Divide and Conquer]]
- **Category:** Geometric divide-conquer with strip optimization

## Pattern Selection
This is the classic Divide and Conquer application demonstrating non-trivial combine step (checking the "strip" near the dividing line).

## Why This Approach Works
**Key Insight:** After finding minimum distances in left and right halves, only need to check points within distance d of the dividing line (the "strip"), and within that strip, only need to check a constant number of neighbors per point (geometric argument).

## Python Solution
```python
import math

def closestPair(points: list[tuple]) -> float:
    """
    Find minimum distance between any two points using divide-conquer.
    Time: O(n log n)
    Space: O(n) for auxiliary arrays
    """
    def dist(p1, p2):
        return math.sqrt((p1[0]-p2[0])**2 + (p1[1]-p2[1])**2)
    
    def bruteForce(pts):
        min_d = float('inf')
        for i in range(len(pts)):
            for j in range(i+1, len(pts)):
                min_d = min(min_d, dist(pts[i], pts[j]))
        return min_d
    
    def closestUtil(px, py):
        n = len(px)
        if n <= 3:
            return bruteForce(px)
        
        mid = n // 2
        midpoint = px[mid]
        
        pyl = [p for p in py if p[0] <= midpoint[0]]
        pyr = [p for p in py if p[0] > midpoint[0]]
        
        dl = closestUtil(px[:mid], pyl)
        dr = closestUtil(px[mid:], pyr)
        d = min(dl, dr)
        
        # Check strip near the dividing line
        strip = [p for p in py if abs(p[0] - midpoint[0]) < d]
        
        for i in range(len(strip)):
            j = i + 1
            while j < len(strip) and (strip[j][1] - strip[i][1]) < d:
                d = min(d, dist(strip[i], strip[j]))
                j += 1
        
        return d
    
    px = sorted(points, key=lambda p: p[0])
    py = sorted(points, key=lambda p: p[1])
    
    return closestUtil(px, py)
```

## Reasoning
1. **Sort by X:** Enables clean left/right split
2. **Divide:** Split points into left and right halves
3. **Conquer:** Recursively find minimum distance in each half
4. **Combine (Strip Check):** Points near dividing line might have closer pairs across the split—check only points within current minimum distance of the line

## Complexity Analysis
- **Time:** O(n log n) — geometric argument limits strip checking to O(n) per level
- **Space:** O(n) — auxiliary arrays for sorted points

## Common Mistakes
1. **Naive O(n²) Strip Check:** Without limiting to nearby y-coordinates, strip check becomes O(n²)
2. **Not Sorting by Y in Strip:** Points must be sorted by y-coordinate for the strip optimization to work correctly

## Interview Walkthrough
**Interviewer:** "Find closest pair of points, better than O(n²)."

**Your Response:**
1. "I'll divide points by x-coordinate into left and right halves."
2. "Recursively find minimum distance in each half."
3. "Critical step: check the 'strip' near the dividing line, since closest pair might straddle the split."
4. "Geometric argument limits strip checking to O(n) total, giving O(n log n) overall."

## Related Problems
- [[Divide and Conquer Representative Problems]]

## Related Concepts
- [[Divide and Conquer]] — core pattern with non-trivial combine step


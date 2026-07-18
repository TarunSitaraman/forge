---
type: python-template
status: stable
tags: [dsa/template, python, dsa/matrix-traversal]
canonical: true
related: [[Matrix Traversal]]
---
# Python - Matrix Traversal

## Purpose
Traverse grid cells with explicit bounds and directions.

## Explanation
This template captures the reusable implementation shape for [[Matrix Traversal]]. Keep problem-specific naming and validation in the problem page, but preserve the invariant and boundary discipline shown here.

## Complexity
O(rows * cols) time and space in worst case.

## Code
``python
from collections import deque

def flood_fill(grid, start):
    rows, cols = len(grid), len(grid[0])
    q = deque([start])
    seen = {start}
    directions = [(1, 0), (-1, 0), (0, 1), (0, -1)]
    while q:
        r, c = q.popleft()
        for dr, dc in directions:
            nr, nc = r + dr, c + dc
            if 0 <= nr < rows and 0 <= nc < cols and (nr, nc) not in seen:
                seen.add((nr, nc))
                q.append((nr, nc))
    return seen
``

## Edge Cases
- Empty input
- Single-element input
- Duplicate values or repeated states
- Inclusive versus exclusive boundary semantics

## Modifications
Use multi-source queues for nearest-distance problems; include diagonal directions only when allowed.

## Common Interview Variations
- Return the count instead of the object
- Return all valid objects instead of the best one
- Add online updates or streaming input
- Tighten memory from O(n) to O(1) when dependencies allow it

## Common Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Infinite Loop]]


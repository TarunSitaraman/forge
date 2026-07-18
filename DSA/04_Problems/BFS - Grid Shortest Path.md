---
type: problem
status: stable
tags: [dsa/problem, dsa/bfs, dsa/grid]
canonical: true
related: [[[BFS]], [[Matrix Traversal]], [[Python - BFS]]]
difficulty: medium
---
# BFS - Grid Shortest Path (Binary Matrix)

## Problem Statement
Given an n×n binary matrix, find the length of the shortest clear path from top-left to bottom-right cell. A clear path consists of cells with value 0, and you can move in all 8 directions (including diagonals). Return -1 if no clear path exists.

**Constraints:**
- 1 ≤ n ≤ 100
- grid[i][j] is 0 (clear) or 1 (blocked)
- Path length counts number of cells visited (including start and end)
- 8-directional movement allowed (unlike typical 4-directional grid problems)

## Classification
- **Patterns:** [[BFS]], [[Matrix Traversal]]
- **Template:** [[Python - BFS]]
- **Category:** Grid pathfinding with obstacles, 8-directional movement

## Pattern Selection
This is canonical for BFS grid pathfinding because:
1. Unweighted grid (each move costs 1) — BFS guarantees shortest path
2. Obstacles (blocked cells) require boundary/validity checking
3. 8-directional movement extends beyond typical 4-directional grid problems
4. Start and end cells must both be clear (0) for path to be possible

## Why BFS Works
**Invariant:** First time reaching the target cell via BFS guarantees minimum path length.

**8-Directional Consideration:**
Unlike typical grid problems (4 directions: up/down/left/right), this allows diagonal movement, adding 4 more directions. This doesn't change BFS's correctness—just expands the neighbor set.

## Python Solution
```python
from collections import deque

def shortestPathBinaryMatrix(grid: list[list[int]]) -> int:
    """
    Find shortest path in binary matrix with 8-directional movement.
    Time: O(n^2) each cell visited once
    Space: O(n^2) worst case queue and visited set
    """
    n = len(grid)
    
    # Edge case: start or end blocked
    if grid[0][0] == 1 or grid[n-1][n-1] == 1:
        return -1
    
    # Edge case: single cell grid
    if n == 1:
        return 1
    
    # 8 directions: horizontal, vertical, diagonal
    directions = [(-1,-1), (-1,0), (-1,1),
                  (0,-1),          (0,1),
                  (1,-1),  (1,0),  (1,1)]
    
    queue = deque([(0, 0, 1)])  # (row, col, path_length)
    visited = {(0, 0)}
    
    while queue:
        r, c, length = queue.popleft()
        
        # Reached target
        if r == n-1 and c == n-1:
            return length
        
        for dr, dc in directions:
            nr, nc = r + dr, c + dc
            
            if (0 <= nr < n and 0 <= nc < n and
                grid[nr][nc] == 0 and
                (nr, nc) not in visited):
                
                visited.add((nr, nc))
                queue.append((nr, nc, length + 1))
    
    return -1  # No path found
```

## Reasoning
1. **Edge Cases First:** Check if start/end blocked, or if grid is single cell
2. **8 Directions:** Include diagonal movements in direction list
3. **Track Path Length:** Store length in queue tuple (avoids separate distance array)
4. **Visited Set:** Prevent revisiting cells (essential for avoiding infinite loops)
5. **Early Termination:** Return immediately upon reaching target (BFS guarantees shortest)
6. **No Path Case:** Return -1 if queue empties without reaching target

## Complexity Analysis
- **Time:** O(n²)
  - Each cell visited at most once
  - 8 directions checked per cell: O(8) = O(1) per cell

- **Space:** O(n²)
  - Visited set: up to n² cells
  - Queue: up to n² cells in worst case

## Edge Cases & Validation
```python
# Start blocked
assert shortestPathBinaryMatrix([[1, 0], [0, 0]]) == -1

# End blocked
assert shortestPathBinaryMatrix([[0, 0], [0, 1]]) == -1

# Single cell, clear
assert shortestPathBinaryMatrix([[0]]) == 1

# Single cell, blocked
assert shortestPathBinaryMatrix([[1]]) == -1

# Simple diagonal path
grid = [[0, 1], [1, 0]]
assert shortestPathBinaryMatrix(grid) == 2  # Diagonal move works

# No path (completely blocked)
grid = [[0, 1, 1], [1, 1, 1], [1, 1, 0]]
assert shortestPathBinaryMatrix(grid) == -1

# Larger grid with clear diagonal path
grid = [[0, 0, 0], [1, 1, 0], [1, 1, 0]]
assert shortestPathBinaryMatrix(grid) == 4
```

## Common Mistakes
1. **[[Missing Visited Set]]:** Not tracking visited cells
   - Causes revisiting and infinite loops (especially problematic with 8 directions)

2. **4-Directional Instead of 8-Directional:**
   - Forgetting diagonal moves changes correct answer
   - Must include all 8 direction tuples

3. **Not Checking Start/End Validity:**
   - Missing edge case where start or end cell is itself blocked
   - Must check `grid[0][0] == 1 or grid[n-1][n-1] == 1` first

4. **[[Off-by-One]]:** Path length calculation
   - Starting length should be 1 (counting starting cell itself)
   - Not 0, since path includes both endpoints

5. **Visited Marking Timing:**
   - Mark visited when ADDING to queue, not when PROCESSING
   - Prevents same cell being added multiple times before processing

## Interview Walkthrough
**Interviewer:** "Find shortest path in binary matrix with diagonal movement allowed."

**Your Response:**
1. "Since all moves cost the same, BFS guarantees shortest path when first reaching target."
2. "I include all 8 directions—horizontal, vertical, and diagonal—as valid neighbors."
3. "I track visited cells and path length together, avoiding a separate distance array."
4. "First time I reach the target, I return immediately since BFS guarantees this is shortest."

**Why This Resonates:**
- Explains why BFS works for unweighted (equal-cost) moves
- Highlights the 8-directional detail (easy to miss/forget)
- Shows efficient state tracking in queue tuples

## Variations
1. **4-Directional Version:** Remove diagonal directions, same algorithm otherwise
2. **Weighted Grid:** Would require Dijkstra instead of simple BFS
3. **Multiple Targets:** Find shortest path to ANY of several target cells
4. **Path Reconstruction:** Track parent pointers to return actual path, not just length

## Related Problems
- [[BFS - Multi-Source Distance]] (similar grid BFS, different problem structure)
- [[BFS - Shortest Path Unweighted]] (general graph version)
- [[BFS Representative Problems]]

## Related Concepts
- [[BFS]] — fundamental pattern
- [[Matrix Traversal]] — grid-specific navigation
- [[8-Directional Movement]] — extended neighbor consideration


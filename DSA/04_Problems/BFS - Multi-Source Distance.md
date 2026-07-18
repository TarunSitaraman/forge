---
type: problem
status: stable
tags: [dsa/problem, dsa/bfs, dsa/grid]
canonical: true
related: [[[BFS]], [[Matrix Traversal]], [[Python - BFS]]]
difficulty: medium
---
# BFS - Multi-Source Distance (Rotting Oranges)

## Problem Statement
Given a grid where each cell can be empty (0), fresh orange (1), or rotten orange (2), determine the minimum time for ALL fresh oranges to become rotten. Rotten oranges spread to adjacent (4-directional) fresh oranges each minute. Return -1 if impossible.

**Constraints:**
- Multiple rotten oranges may exist initially (multiple sources)
- Grid boundaries define the search space
- Return 0 if no fresh oranges exist initially

## Classification
- **Patterns:** [[BFS]], [[Matrix Traversal]]
- **Template:** [[Python - BFS]]
- **Category:** Multi-source simultaneous BFS with time tracking

## Pattern Selection
This is canonical for Multi-Source BFS because:
1. Multiple starting points (all initially rotten oranges) spread simultaneously
2. Single-source BFS from each rotten orange would give wrong (non-simultaneous) timing
3. All sources must start in queue together, processing level-by-level = minute-by-minute
4. Distance/time from nearest source determines rotting time for each cell

## Why Multi-Source BFS Works
**Invariant:** All oranges rotting at minute t must be processed before any orange rotting at minute t+1.

**Key Insight:**
- Initialize queue with ALL rotten oranges simultaneously (not one at a time)
- Each BFS "layer" represents one minute passing
- Track minutes via level-boundary technique (like level-order traversal)

## Python Solution
```python
from collections import deque

def orangesRotting(grid: list[list[int]]) -> int:
    """
    Find minimum time for all oranges to rot using multi-source BFS.
    Time: O(m*n) each cell processed once
    Space: O(m*n) worst case queue size
    """
    if not grid or not grid[0]:
        return 0
    
    rows, cols = len(grid), len(grid[0])
    queue = deque()
    fresh_count = 0
    
    # Initialize: find all rotten oranges (multiple sources) and count fresh
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == 2:
                queue.append((r, c))
            elif grid[r][c] == 1:
                fresh_count += 1
    
    # Edge case: no fresh oranges means already done
    if fresh_count == 0:
        return 0
    
    minutes = 0
    directions = [(1, 0), (-1, 0), (0, 1), (0, -1)]
    
    while queue and fresh_count > 0:
        minutes += 1
        level_size = len(queue)
        
        for _ in range(level_size):
            r, c = queue.popleft()
            
            for dr, dc in directions:
                nr, nc = r + dr, c + dc
                
                if (0 <= nr < rows and 0 <= nc < cols and
                    grid[nr][nc] == 1):
                    grid[nr][nc] = 2  # Rot this orange
                    fresh_count -= 1
                    queue.append((nr, nc))
    
    # If fresh oranges remain, impossible to rot all
    return minutes if fresh_count == 0 else -1
```

## Reasoning
1. **Initialize Multi-Source Queue:** Add ALL rotten oranges to queue simultaneously
2. **Count Fresh Oranges:** Track total fresh count for termination check
3. **Edge Case:** If no fresh oranges exist, return 0 immediately
4. **Process by Minute:** Each iteration of outer while loop = one minute
5. **Level Boundary:** Process exactly `level_size` oranges per minute (multi-source BFS)
6. **Spread Rot:** For each rotten orange, check 4 neighbors; rot if fresh
7. **Termination:** Loop ends when queue empties; check if fresh_count is 0

## Complexity Analysis
- **Time:** O(m·n)
  - Each cell added to queue at most once
  - Each cell's neighbors checked once when processed

- **Space:** O(m·n)
  - Queue can hold up to m·n cells in worst case (all initially rotten)

## Edge Cases & Validation
```python
# No fresh oranges
grid = [[2, 2], [2, 2]]
assert orangesRotting(grid) == 0

# All fresh, no rotten (impossible)
grid = [[1, 1], [1, 1]]
assert orangesRotting(grid) == -1

# Simple spread
grid = [[2, 1, 1], [1, 1, 0], [0, 1, 1]]
assert orangesRotting(grid) == 4

# Isolated fresh orange (unreachable)
grid = [[2, 1, 1], [0, 0, 0], [1, 1, 1]]  # Wall of 0s blocks bottom row
assert orangesRotting(grid) == -1

# Multi-source: two rotten oranges spread simultaneously
grid = [[2, 0, 0, 0, 2],
        [1, 1, 1, 1, 1]]
result = orangesRotting(grid)
assert result == 2  # Faster than single-source would suggest

# Empty grid
assert orangesRotting([[0]]) == 0
```

## Common Mistakes
1. **Sequential Single-Source BFS:** Running BFS from each rotten orange separately
   - Wrong: gives incorrect simultaneous timing
   - Correct: initialize ALL sources in queue together

2. **[[Boundary Errors]]:** Not checking grid bounds before accessing neighbors
   - Must verify `0 <= nr < rows and 0 <= nc < cols`

3. **Not Tracking Fresh Count:** 
   - Need to verify ALL fresh oranges rotted (not just that queue emptied)
   - Isolated fresh oranges (surrounded by walls/empty) never rot

4. **Minute Counting Off-By-One:**
   - Increment minutes at START of each level processing
   - If no more processing needed (fresh_count == 0 already), shouldn't increment further

5. **Modifying Grid During Iteration:**
   - Marking as rotten (2) immediately when adding to queue prevents double-processing

## Interview Walkthrough
**Interviewer:** "Find minimum time for all oranges to rot."

**Your Response:**
1. "Multiple oranges start rotten—these are multiple simultaneous BFS sources."
2. "I initialize the queue with ALL rotten oranges at once, not one at a time."
3. "Each BFS level represents one minute; I process exactly the current level's oranges."
4. "I track fresh count to detect if some oranges are unreachable (isolated)."

**Why This Resonates:**
- Explains WHY multi-source (not sequential single-source)
- Shows the level-boundary technique from tree traversal applied here
- Demonstrates awareness of unreachable/isolated cell edge case

## Variations
1. **Walls and Gates:** Similar multi-source BFS, but fill distances instead of counting time
2. **01 Matrix:** Find distance to nearest 0 for each cell (multi-source from all 0s)
3. **Shortest Bridge:** Connect two islands with minimum bridge (BFS + multi-source combination)
4. **Farthest Land from Water:** Maximize distance instead of minimize

## Related Problems
- [[BFS - Shortest Path Unweighted]] (single-source variant)
- [[BFS - Grid Shortest Path]] (obstacle-based grid traversal)
- [[BFS Representative Problems]]

## Related Concepts
- [[BFS]] — fundamental pattern extended to multi-source
- [[Matrix Traversal]] — grid-specific context
- [[Queue]] — enables simultaneous multi-source processing


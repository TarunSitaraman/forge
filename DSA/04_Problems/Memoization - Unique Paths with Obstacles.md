---
type: problem
status: stable
tags: [dsa/problem, dsa/memoization, dsa/dynamic-programming]
canonical: true
related: [[[Memoization]], [[Dynamic Programming]]]
difficulty: medium
---
# Memoization - Unique Paths with Obstacles

## Problem Statement
Given an m×n grid with obstacles (1) and open cells (0), find the number of unique paths from top-left to bottom-right, moving only right or down, avoiding obstacles.

**Constraints:**
- Grid may have 0 valid paths (if start/end blocked or path impossible)
- Grid contains only 0 (open) and 1 (obstacle)

## Classification
- **Patterns:** [[Memoization]], [[Dynamic Programming]]
- **Category:** Grid path counting with state caching

## Pattern Selection
This is canonical for Memoization because naive recursion re-explores the same (row, col) positions repeatedly via different paths, causing exponential blowup without caching.

## Why Memoization Works
**Key Insight:** paths(r, c) depends only on paths(r+1, c) and paths(r, c+1)—same subproblems recur via different paths through the grid. Caching by (row, col) position eliminates redundant computation.

## Python Solution
```python
def uniquePathsWithObstacles(obstacleGrid: list[list[int]]) -> int:
    """
    Count unique paths avoiding obstacles using memoized recursion.
    Time: O(m*n) with memoization
    Space: O(m*n) for cache + recursion depth
    """
    if not obstacleGrid or obstacleGrid[0][0] == 1:
        return 0
    
    rows, cols = len(obstacleGrid), len(obstacleGrid[0])
    memo = {}
    
    def paths(r, c):
        if r >= rows or c >= cols or obstacleGrid[r][c] == 1:
            return 0
        
        if r == rows - 1 and c == cols - 1:
            return 1  # Reached destination
        
        if (r, c) in memo:
            return memo[(r, c)]
        
        result = paths(r + 1, c) + paths(r, c + 1)
        memo[(r, c)] = result
        return result
    
    return paths(0, 0)
```

## Reasoning
1. **State Definition:** paths(r, c) = number of ways to reach destination from (r, c)
2. **Base Cases:** Out of bounds or obstacle → 0 paths; destination reached → 1 path
3. **Transition:** paths(r, c) = paths(r+1, c) + paths(r, c+1) (can only move right or down)
4. **Memoization:** Cache by (row, col) tuple to avoid recomputing same position multiple times

## Complexity Analysis
- **Time:** O(m·n) — each (row, col) position computed once due to memoization
- **Space:** O(m·n) — memoization cache, plus O(m+n) recursion depth

## Edge Cases & Validation
```python
assert uniquePathsWithObstacles([[0,0,0],[0,1,0],[0,0,0]]) == 2
assert uniquePathsWithObstacles([[0,1],[0,0]]) == 1
assert uniquePathsWithObstacles([[1,0]]) == 0  # Start blocked
assert uniquePathsWithObstacles([[0]]) == 1  # Single cell, no obstacle
```

## Common Mistakes
1. **No Memoization:** Naive recursion becomes exponential O(2^(m+n)) without caching
2. **Wrong Cache Key:** Using string concatenation instead of tuple (r,c) is less efficient
3. **Not Checking Start/End for Obstacles:** Missing edge case where start or end position itself is blocked

## Interview Walkthrough
**Interviewer:** "Count unique paths in grid avoiding obstacles."

**Your Response:**
1. "Naive recursion re-explores same grid positions through different paths—I'll add memoization."
2. "State: paths(r,c) = ways to reach destination from this position."
3. "Transition: sum of paths going right and paths going down (only 2 valid moves)."
4. "Caching by (row, col) position transforms exponential time to O(m*n)."

## Related Problems
- [[Dynamic Programming - Longest Common Subsequence]] (similar 2D DP but different domain)
- [[Memoization - Word Break]] (different problem, same caching pattern)
- [[Memoization Representative Problems]]

## Related Concepts
- [[Memoization]] — top-down caching technique
- [[Dynamic Programming]] — equivalent bottom-up formulation possible


---
type: problem
status: stable
tags: [dsa/problem, dsa/dfs, dsa/graph]
canonical: true
related: [[[DFS]], [[Graph Traversal]], [[Python - DFS Recursive]]]
difficulty: medium
---
# DFS - Number of Islands

## Problem Statement
Given an m×n 2D grid map of '1's (land) and '0's (water), return the number of islands.

An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

**Constraints:**
- 1 ≤ m, n ≤ 1000
- Grid contains only '0' and '1'
- Adjacent: horizontal/vertical (4-directional, not diagonal)

## Classification
- **Patterns:** [[DFS]], [[Graph Traversal]]
- **Template:** [[Python - DFS Recursive]]
- **Category:** Connected components counting in grid

## Pattern Selection
This is canonical for DFS because:
1. Grid acts as graph with implicit edges (adjacent cells)
2. Need to find all connected components (islands)
3. DFS naturally explores entire island in one traversal
4. No need for explicit adjacency list; directions are implicit

Alternative: BFS works equally well; both find connected components in O(V+E) time.

## Why DFS Works
**Invariant:** All cells belonging to an island are visited in one DFS traversal.

**Strategy:**
- For each unvisited '1', start DFS
- Mark as visited; explore all 4 neighbors recursively
- Each DFS completes one full island
- Count number of DFS calls = number of islands

**Why it's correct:**
- Connectivity is guaranteed by 4-directional movement
- No cell is visited twice (marked with 0 or visited flag)
- Each island forms one connected component

## Python Solution
```python
def numIslands(grid: list[list[str]]) -> int:
    """
    Count number of islands using DFS.
    Time: O(m*n) visit each cell once
    Space: O(m*n) worst-case recursion depth (all land)
    """
    if not grid or not grid[0]:
        return 0
    
    rows, cols = len(grid), len(grid[0])
    count = 0
    
    def dfs(r, c):
        """Mark connected land as visited."""
        # Base case: boundary check
        if r < 0 or r >= rows or c < 0 or c >= cols:
            return
        
        # Base case: already water or visited
        if grid[r][c] == '0':
            return
        
        # Mark as visited (convert to water to avoid revisit)
        grid[r][c] = '0'
        
        # Explore all 4 neighbors (recursion)
        dfs(r + 1, c)  # Down
        dfs(r - 1, c)  # Up
        dfs(r, c + 1)  # Right
        dfs(r, c - 1)  # Left
    
    # Find all islands
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':  # Found new island
                dfs(r, c)           # Mark entire island
                count += 1          # Count this island
    
    return count
```

## Reasoning
1. **Scan Grid:** Iterate through each cell in the grid
2. **Find Unvisited Land:** When we encounter '1', it's a new island
3. **Explore Island:** Use DFS to visit all connected land
4. **Mark Visited:** Convert to '0' to prevent revisiting
5. **Count:** Increment counter for each new island found
6. **Return:** Total count of islands found

## Complexity Analysis
- **Time:** O(m·n)
  - Each cell visited exactly once
  - Each cell processed in constant time
  - Total: m·n cells

- **Space:** O(m·n) worst-case
  - Recursion depth: maximum m·n if all cells are land forming one diagonal
  - Call stack can grow to O(m+n) typical; O(m·n) worst case
  - No extra data structures

## Edge Cases & Validation
```python
# Empty grid
assert numIslands([]) == 0

# Single cell
assert numIslands([['1']]) == 1
assert numIslands([['0']]) == 0

# Single island
assert numIslands([['1', '1'], ['1', '1']]) == 1

# Multiple islands
assert numIslands([['1', '0', '1'], ['0', '1', '0'], ['1', '0', '1']]) == 5

# Diagonal doesn't count as connected
assert numIslands([['1', '0'], ['0', '1']]) == 2

# Large single island
grid = [['1'] * 100 for _ in range(100)]
assert numIslands(grid) == 1

# Alternating pattern
grid = [['1' if (i + j) % 2 == 0 else '0' for j in range(10)] for i in range(10)]
assert numIslands(grid) == 50  # Checkerboard pattern
```

## Common Mistakes
1. **[[Boundary Errors]]:** Not checking grid bounds before accessing
   - Missing `r < 0 or r >= rows` checks
   - Causes IndexError

2. **[[Missing Visited Set]]:** Not marking cells as visited
   - Without marking, revisit same cell infinitely
   - Stack overflow from infinite recursion

3. **[[Infinite Loop]]:** Improper visited tracking
   - If don't modify grid, need separate visited set
   - Otherwise revisit and recurse endlessly

4. **[[Off-by-One]]:** Grid indexing errors
   - Using `rows` instead of `rows-1` in boundary check
   - Can access invalid cells

5. **Not Handling Empty Grid:** 
   - Should return 0 for empty input
   - Missing null/empty checks

## Interview Walkthrough
**Interviewer:** "Count islands in a grid."

**Your Response:**
1. "I'll treat the grid as a graph where '1's are connected nodes."
2. "For each unvisited '1', I'll start a DFS to explore the entire island."
3. "DFS marks all connected cells as visited (converting to '0')."
4. "Each DFS call represents one island, so I count these calls."
5. "Time is O(m·n) - each cell visited once. Space is O(m·n) worst-case recursion."

**Diagram It:**
```
Grid:         After DFS:
1 1 0 1       0 0 0 2
1 0 0 0  -->  0 0 0 0
0 0 1 1       0 0 3 3

Island count: 3 (one call for island 1, one for island 2, one for island 3)
```

**Why This Resonates:**
- Shows you recognize connected components pattern
- Explains how DFS "claims" all connected cells
- Demonstrates understanding of visited tracking
- Notes complexity trade-off (time efficient, space worst-case)

## Variations
1. **Find Largest Island:** Track max size during DFS
2. **Return Island Coordinates:** Collect cells instead of just visiting
3. **8-Connected:** Include diagonal neighbors (8 directions instead of 4)
4. **Island Perimeter:** Count edges on island boundary
5. **Biased Island:** Islands of specific shapes or sizes only

## Interview Variants
**Interviewer:** "Can you do this without modifying the input grid?"

Your Response:
- "Yes, use a separate visited set or boolean array instead of modifying grid"
- Trade-off: O(m·n) space for visited set vs. modifying input

**Interviewer:** "What if you can't use recursion (stack overflow risk)?"

Your Response:
- "Use iterative DFS with explicit stack, or BFS with queue"
- Iterative version: push to stack, pop and explore

**Interviewer:** "Find all islands (return coordinates, not count)?"

Your Response:
- "During DFS, collect coordinates instead of just counting"
- Return list of island cell lists

## Related Problems
- [[DFS - Cycle Detection]] (detect if island formation creates cycle)
- [[Graph Traversal - Number of Islands]] (same problem, different framing)
- [[BFS - Level Order]] (BFS alternative solution)
- [[DFS Representative Problems]]

## Related Concepts
- [[DFS]] — fundamental pattern for this problem
- [[Connected Components]] — what we're counting
- [[Graph Traversal]] — generalization of grid traversal
- [[Visited Tracking]] — critical to prevent infinite loops


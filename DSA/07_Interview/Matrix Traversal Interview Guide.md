---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/matrix-traversal]
canonical: true
related: [[[Matrix Traversal]], [[Coding Interviews]]]
---
# Matrix Traversal Interview Guide

## Pattern Recognition
**Green Flags:** 2D grid problems, spiral/diagonal/zigzag traversal, island counting, path finding in grid

## Communication Template
`I'll traverse using direction vectors (4 or 8 directional), being careful with boundary checks before accessing grid[r][c].`

## Core Mechanics
```python
def traverse_grid(grid):
    rows, cols = len(grid), len(grid[0])
    directions = [(0,1),(1,0),(0,-1),(-1,0)]
    
    def is_valid(r, c):
        return 0 <= r < rows and 0 <= c < cols
    
    for r in range(rows):
        for c in range(cols):
            for dr, dc in directions:
                nr, nc = r+dr, c+dc
                if is_valid(nr, nc):
                    process(grid[nr][nc])
```

## Common Mistakes
1. Off-by-one in boundary checks
2. Forgetting diagonal directions when 8-directional needed
3. Not marking visited cells (infinite loops in DFS/BFS on grid)

## Practice Problems
- Spiral Matrix, Number of Islands, Rotate Image

## Checklist
- [ ] Boundary checks verified correct (< not <=)
- [ ] Direction count matches problem requirement (4 vs 8)
- [ ] Visited tracking present if traversal-based

## Related
- [[Matrix Traversal]]
- [[Matrix Traversal Representative Problems]]

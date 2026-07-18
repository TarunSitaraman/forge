---
type: problem
status: stable
tags: [dsa/problem, dsa/graph-traversal-number-of-islands]
canonical: true
related: [[[Matrix Traversal]], [[DFS]], [[BFS]], [[Python - Matrix Traversal]]]
---
# Graph Traversal - Number of Islands

## Problem Statement
Count connected components of land cells in a grid.

## Classification
- Patterns: [[Matrix Traversal]], [[DFS]], [[BFS]]
- Template: [[Python - Matrix Traversal]]

## Pattern Selection
Grid problems are graph problems with implicit neighbors and strict boundary checks. This page applies canonical theory rather than duplicating it.

## Python Solution
``python
def num_islands(grid):
    if not grid:
        return 0
    rows, cols = len(grid), len(grid[0])
    seen = set()
    def dfs(r, c):
        if r < 0 or r >= rows or c < 0 or c >= cols:
            return
        if grid[r][c] != "1" or (r, c) in seen:
            return
        seen.add((r, c))
        dfs(r + 1, c); dfs(r - 1, c); dfs(r, c + 1); dfs(r, c - 1)
    count = 0
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == "1" and (r, c) not in seen:
                count += 1
                dfs(r, c)
    return count
``

## Reasoning
The solution preserves the invariant owned by the linked pattern page, advances monotonically through the input or state space, and records only the state required to produce the answer.

## Complexity
O(rows*cols) time and O(rows*cols) worst-case space.

## Lessons
- State the invariant before coding.
- Keep boundary handling explicit.
- Link reusable reasoning back to canonical pages.

## Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Missing Visited Set]]

## Related Problems
- [[Problem Index]]


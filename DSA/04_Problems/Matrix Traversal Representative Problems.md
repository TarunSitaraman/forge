---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/matrix-traversal]
canonical: true
---
# Matrix Traversal Representative Problems

Core problem variants exercising Matrix Traversal across different patterns and directions.

## Variant 1: Spiral Traversal
**Recognition:** Traverse matrix in spiral pattern (clockwise or counterclockwise).
**Mechanics:** Track four boundaries; shrink each after traversing.
**Key Challenge:** Boundary management; odd/even dimensions.

- [[Matrix Traversal - Spiral Matrix]]
- Variant: Spiral reverse (given spiral, find original)
- Variant: Spiral collection

## Variant 2: Direction-Based Traversal
**Recognition:** Traverse in specific direction pattern (zigzag, diagonal).
**Mechanics:** Choose direction; follow until boundary; move to next.
**Key Challenge:** Direction changes; coordinate calculations.

- Variant: Zigzag traversal
- Variant: Diagonal traversal
- Variant: Alternate directions

## Variant 3: Path Finding in Grid
**Recognition:** Find optimal path in grid with obstacles/weights.
**Mechanics:** BFS/DFS from start; mark visited; track path.
**Key Challenge:** Obstacle handling; path reconstruction.

- [[Matrix Traversal - Grid Path]]
- Variant: Shortest path with obstacles
- Variant: Maximum sum path
- Variant: Unique paths counting

## Variant 4: Region Processing (Islands)
**Recognition:** Process connected regions (flood fill, island counting).
**Mechanics:** DFS/BFS from each unvisited cell; mark region.
**Key Challenge:** 4-directional vs. 8-directional connectivity.

- [[Matrix Traversal - Number of Islands]]
- Variant: Max island area
- Variant: Surrounded regions marking

## Variant 5: Boundary Traversal
**Recognition:** Access or process boundary elements of matrix.
**Mechanics:** Traverse four edges; track visited.
**Key Challenge:** Corner handling; single row/column matrices.

- Variant: Boundary elements collection
- Variant: Rotate matrix in-place
- Variant: Outer ring processing

## Variant 6: Rotated/Sorted Matrix Search
**Recognition:** Search in rotated or specially sorted 2D matrix.
**Mechanics:** Treat as sorted 1D (binary search); map indices.
**Key Challenge:** Index mapping; rotation point detection.

- Variant: Search in rotated matrix
- Variant: Search in row/column sorted matrix
- Variant: Peak element in matrix

## Cross-Linking
- **Pattern:** [[Matrix Traversal]]
- **Related Patterns:** [[DFS]], [[BFS]], [[Binary Search]]
- **Interview Guide:** [[Matrix Traversal Interview Guide]]

## Common Pitfalls
- Boundary condition errors (off-by-one)
- Forgetting visited tracking (revisits)
- Wrong direction index (row/col swapped)
- Spiral boundary shrinking order


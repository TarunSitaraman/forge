---
type: problem
status: stable
tags: [dsa/problem, dsa/matrix-traversal]
canonical: true
related: [[[Matrix Traversal]]]
difficulty: medium
---
# Matrix Traversal - Rotate Image

## Problem Statement
Given an n×n 2D matrix representing an image, rotate it 90 degrees clockwise in-place.

**Constraints:**
- Must rotate in-place (O(1) extra space, not counting output)
- Matrix is square (n×n)

## Classification
- **Patterns:** [[Matrix Traversal]]
- **Category:** In-place matrix transformation

## Pattern Selection
This is canonical for Matrix Traversal because it demonstrates the elegant two-step transformation: transpose + reverse rows = 90° clockwise rotation, avoiding complex layer-by-layer rotation logic.

## Why This Approach Works
**Key Insight:** A 90° clockwise rotation can be decomposed into two simpler operations:
1. Transpose the matrix (swap matrix[i][j] with matrix[j][i])
2. Reverse each row

This is mathematically equivalent to direct rotation but much easier to implement correctly.

## Python Solution
```python
def rotate(matrix: list[list[int]]) -> None:
    """
    Rotate matrix 90 degrees clockwise in-place.
    Time: O(n^2)
    Space: O(1) - in-place
    """
    n = len(matrix)
    
    # Step 1: Transpose (swap across main diagonal)
    for i in range(n):
        for j in range(i + 1, n):
            matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
    
    # Step 2: Reverse each row
    for row in matrix:
        row.reverse()


def rotateLayerByLayer(matrix: list[list[int]]) -> None:
    """
    Alternative: direct layer-by-layer rotation (4-way swap).
    """
    n = len(matrix)
    for layer in range(n // 2):
        first, last = layer, n - 1 - layer
        for i in range(first, last):
            offset = i - first
            top = matrix[first][i]
            
            # left -> top
            matrix[first][i] = matrix[last-offset][first]
            # bottom -> left
            matrix[last-offset][first] = matrix[last][last-offset]
            # right -> bottom
            matrix[last][last-offset] = matrix[i][last]
            # top -> right
            matrix[i][last] = top
```

## Reasoning
1. **Transpose:** Swap element (i,j) with (j,i) for all i<j — converts rows to columns
2. **Reverse Rows:** After transpose, reversing each row completes the 90° clockwise rotation
3. **Why It Works:** Transpose flips along diagonal; reversing rows then flips horizontally, combining to a net 90° clockwise rotation

## Complexity Analysis
- **Time:** O(n²) — visit each element a constant number of times
- **Space:** O(1) — in-place, only temp variables for swapping

## Edge Cases & Validation
```python
matrix = [[1,2,3],[4,5,6],[7,8,9]]
rotate(matrix)
assert matrix == [[7,4,1],[8,5,2],[9,6,3]]

matrix2 = [[1]]
rotate(matrix2)
assert matrix2 == [[1]]

matrix3 = [[1,2],[3,4]]
rotate(matrix3)
assert matrix3 == [[3,1],[4,2]]
```

## Common Mistakes
1. **Wrong Transpose Range:** Using `range(n)` instead of `range(i+1, n)` for inner loop causes double-swapping (undoes the transpose)
2. **Reversing Before Transposing:** Order matters—transpose first, then reverse
3. **Confusing Clockwise with Counter-Clockwise:** Counter-clockwise would need reverse-then-transpose, or transpose-then-reverse-columns

## Interview Walkthrough
**Interviewer:** "Rotate matrix 90 degrees clockwise, in-place."

**Your Response:**
1. "Direct rotation logic is complex with 4-way swaps per layer."
2. "Simpler approach: decompose into transpose + row reversal."
3. "Transpose swaps matrix[i][j] with matrix[j][i]—converts rows to columns."
4. "Reversing each row after transpose completes the clockwise rotation."

**Why This Resonates:**
- Shows preference for elegant mathematical decomposition over complex direct manipulation
- Demonstrates understanding of matrix transformation composition

## Variations
1. **Rotate 90° Counter-Clockwise:** Reverse columns then transpose (or transpose then reverse columns)
2. **Rotate 180°:** Reverse both rows and columns
3. **Non-Square Matrix Rotation:** Requires different output dimensions (not in-place possible)

## Related Problems
- [[Matrix Traversal - Spiral Matrix]] (different traversal pattern)
- [[Matrix Traversal Representative Problems]]

## Related Concepts
- [[Matrix Traversal]] — in-place transformation technique


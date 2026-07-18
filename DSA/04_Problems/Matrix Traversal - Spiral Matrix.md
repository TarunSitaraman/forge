---
type: problem
status: stable
tags: [dsa/problem, dsa/matrix-traversal]
canonical: true
related: [[[Matrix Traversal]]]
difficulty: medium
---
# Matrix Traversal - Spiral Matrix

## Problem Statement
Given an m×n matrix, return all elements in spiral order (clockwise from top-left).

**Constraints:**
- 1 ≤ m, n ≤ 10
- Matrix may not be square

## Classification
- **Patterns:** [[Matrix Traversal]]
- **Category:** Boundary-shrinking directional traversal

## Pattern Selection
This is canonical for Matrix Traversal because it requires careful boundary tracking across four shrinking edges (top, bottom, left, right) in a specific rotation order.

## Why This Approach Works
**Key Insight:** Maintain four boundaries (top, bottom, left, right). Traverse each edge, then shrink that boundary inward. Repeat until boundaries cross.

## Python Solution
```python
def spiralOrder(matrix: list[list[int]]) -> list[int]:
    """
    Traverse matrix in spiral order using boundary tracking.
    Time: O(m*n) visit each element once
    Space: O(1) excluding output
    """
    if not matrix or not matrix[0]:
        return []
    
    result = []
    top, bottom = 0, len(matrix) - 1
    left, right = 0, len(matrix[0]) - 1
    
    while top <= bottom and left <= right:
        # Traverse right along top row
        for col in range(left, right + 1):
            result.append(matrix[top][col])
        top += 1
        
        # Traverse down along right column
        for row in range(top, bottom + 1):
            result.append(matrix[row][right])
        right -= 1
        
        # Traverse left along bottom row (if still valid)
        if top <= bottom:
            for col in range(right, left - 1, -1):
                result.append(matrix[bottom][col])
            bottom -= 1
        
        # Traverse up along left column (if still valid)
        if left <= right:
            for row in range(bottom, top - 1, -1):
                result.append(matrix[row][left])
            left += 1
    
    return result
```

## Reasoning
1. **Four Boundaries:** Track top, bottom, left, right edges
2. **Traverse Top Row:** Left to right, then shrink top boundary
3. **Traverse Right Column:** Top to bottom, then shrink right boundary
4. **Traverse Bottom Row (if valid):** Right to left, then shrink bottom
5. **Traverse Left Column (if valid):** Bottom to top, then shrink left
6. **Validity Checks:** Must verify top<=bottom and left<=right before bottom/left traversals (handles single row/column edge cases)

## Complexity Analysis
- **Time:** O(m·n) — visit each element exactly once
- **Space:** O(1) — excluding output array

## Edge Cases & Validation
```python
# Square matrix
matrix = [[1,2,3],[4,5,6],[7,8,9]]
assert spiralOrder(matrix) == [1,2,3,6,9,8,7,4,5]

# Single row
matrix = [[1,2,3,4]]
assert spiralOrder(matrix) == [1,2,3,4]

# Single column
matrix = [[1],[2],[3]]
assert spiralOrder(matrix) == [1,2,3]

# Rectangular (more rows than cols)
matrix = [[1,2],[3,4],[5,6]]
assert spiralOrder(matrix) == [1,2,4,6,5,3]

# Single element
assert spiralOrder([[1]]) == [1]
```

## Common Mistakes
1. **Missing Validity Checks:** Not checking top<=bottom or left<=right before bottom/left traversals causes duplicate elements in single row/column cases
2. **[[Boundary Errors]]:** Off-by-one in range boundaries
3. **Wrong Traversal Order:** Must be top→right→bottom→left consistently

## Interview Walkthrough
**Interviewer:** "Traverse matrix in spiral order."

**Your Response:**
1. "I'll track four shrinking boundaries: top, bottom, left, right."
2. "Traverse each edge in order (top row, right column, bottom row, left column), shrinking boundaries as I go."
3. "Critical detail: must validate boundaries still valid before bottom/left traversals to avoid duplicates in edge cases like single row."

## Related Problems
- [[Matrix Traversal Representative Problems]]

## Related Concepts
- [[Matrix Traversal]] — core pattern


---
type: problem
status: stable
tags: [dsa/problem, dsa/backtracking]
canonical: true
related: [[[Backtracking]], [[DFS]]]
difficulty: hard
---
# Backtracking - N-Queens

## Problem Statement
Place N queens on an N×N chessboard such that no two queens attack each other (same row, column, or diagonal). Return all distinct solutions.

**Constraints:**
- 1 ≤ n ≤ 9
- Queens attack along rows, columns, and both diagonals
- Return all valid board configurations

## Classification
- **Patterns:** [[Backtracking]], [[DFS]]
- **Category:** Constraint satisfaction with position-based validation

## Pattern Selection
This is canonical for Backtracking because:
1. Classic constraint satisfaction problem (CSP)
2. Place queens row by row, checking constraints before placing
3. Backtrack immediately when constraint violated (pruning)
4. Efficient validation using sets for columns/diagonals instead of checking entire board

## Why Backtracking Works
**Key Optimization - O(1) Constraint Checking:**
- Since one queen per row, only need to check: column conflicts, diagonal conflicts
- Use sets to track occupied columns, and both diagonal directions
- Diagonal identification: (row - col) constant for one diagonal, (row + col) constant for other

## Python Solution
```python
def solveNQueens(n: int) -> list[list[str]]:
    """
    Find all valid N-Queens placements using backtracking.
    Time: O(N!) worst case, heavily pruned in practice
    Space: O(N) for tracking sets + O(N) recursion depth
    """
    result = []
    cols = set()
    diag1 = set()  # row - col (identifies "/" diagonal)
    diag2 = set()  # row + col (identifies "\" diagonal)
    board = [['.' for _ in range(n)] for _ in range(n)]
    
    def backtrack(row):
        if row == n:
            # Found valid solution
            result.append([''.join(r) for r in board])
            return
        
        for col in range(n):
            # Check if position is valid (O(1) using sets)
            if col in cols or (row - col) in diag1 or (row + col) in diag2:
                continue  # Constraint violated, skip
            
            # Place queen
            cols.add(col)
            diag1.add(row - col)
            diag2.add(row + col)
            board[row][col] = 'Q'
            
            # Recurse to next row
            backtrack(row + 1)
            
            # Backtrack: remove queen
            cols.remove(col)
            diag1.remove(row - col)
            diag2.remove(row + col)
            board[row][col] = '.'
    
    backtrack(0)
    return result


def totalNQueens(n: int) -> int:
    """
    Count solutions without storing board states (memory-efficient).
    """
    cols = set()
    diag1 = set()
    diag2 = set()
    count = [0]
    
    def backtrack(row):
        if row == n:
            count[0] += 1
            return
        
        for col in range(n):
            if col in cols or (row - col) in diag1 or (row + col) in diag2:
                continue
            
            cols.add(col)
            diag1.add(row - col)
            diag2.add(row + col)
            
            backtrack(row + 1)
            
            cols.remove(col)
            diag1.remove(row - col)
            diag2.remove(row + col)
    
    backtrack(0)
    return count[0]
```

## Reasoning
1. **Row-by-Row Placement:** Since exactly one queen per row, iterate rows, choosing column for each
2. **O(1) Validity Check:** Use three sets (columns, both diagonals) instead of scanning entire board
3. **Diagonal Math:** row-col identifies one diagonal direction, row+col identifies the other
4. **Place-Explore-Backtrack:** Standard backtracking triad
5. **Base Case:** When row == n, all queens placed validly—record solution

## Complexity Analysis
- **Time:** O(N!) worst case (though heavily pruned by constraint checking)
- **Space:** O(N) for the three tracking sets, O(N) recursion depth, O(N²) for board representation

## Edge Cases & Validation
```python
# N=1: trivial single solution
assert len(solveNQueens(1)) == 1

# N=2 or N=3: no valid solutions exist
assert len(solveNQueens(2)) == 0
assert len(solveNQueens(3)) == 0

# N=4: classic example with 2 solutions
assert len(solveNQueens(4)) == 2

# N=8: classic 8-queens problem
assert len(solveNQueens(8)) == 92

# Verify count matches solve
assert totalNQueens(4) == len(solveNQueens(4))
```

## Common Mistakes
1. **O(N) Validity Check Instead of O(1):** Scanning board/previous rows each time instead of using sets
2. **Wrong Diagonal Formula:** Confusing row-col with row+col for the two diagonal directions
3. **Forgetting to Backtrack:** Not removing from sets after exploring (corrupts subsequent branches)
4. **Not Copying Board State:** Appending reference to board instead of creating string snapshot

## Interview Walkthrough
**Interviewer:** "Solve N-Queens problem."

**Your Response:**
1. "Since each row has exactly one queen, I'll place queens row by row."
2. "For O(1) validity checking, I'll track occupied columns and both diagonals using sets."
3. "Diagonal identification: row-col is constant along one diagonal, row+col along the other."
4. "Standard backtracking: place, recurse, then remove (backtrack) to try next column."

**Why This Resonates:**
- Shows the crucial O(1) validity check optimization (vs. naive O(N) scanning)
- Explains the diagonal math clearly (often confusing without explanation)
- Demonstrates efficient backtracking implementation

## Variations
1. **N-Queens II (Count Only):** Simpler - just count solutions, don't store boards (shown above)
2. **Queens Attack Detection:** Simpler subset - just check if two queens attack each other
3. **K-Queens with Obstacles:** Extended constraint - some squares blocked

## Related Problems
- [[Backtracking - Permutations]] (different backtracking application)
- [[Backtracking - Word Search]] (grid-based backtracking with reuse constraint)
- [[Backtracking Representative Problems]]

## Related Concepts
- [[Backtracking]] — choose-explore-unchoose pattern
- [[DFS]] — recursive exploration mechanism
- [[Constraint Satisfaction]] — general CSP framework


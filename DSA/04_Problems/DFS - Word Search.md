---
type: problem
status: stable
tags: [dsa/problem, dsa/dfs, dsa/backtracking, dsa/matrix]
canonical: true
related: [[[DFS]], [[Backtracking]], [[Matrix Traversal]]]
difficulty: medium
---
# DFS - Word Search

## Problem Statement
Given an m×n grid of characters and a target word, determine if the word can be constructed by tracing a path through adjacent cells (horizontally or vertically), where each cell is used at most once.

**Constraints:**
- 1 ≤ m, n ≤ 6
- Word length 1 ≤ length ≤ 15
- Same cell may not be used more than once in a single path

## Classification
- **Patterns:** [[DFS]], [[Backtracking]], [[Matrix Traversal]]
- **Template:** [[Python - DFS Recursive]]
- **Category:** Constrained path search with backtracking in grid

## Pattern Selection
This is canonical for DFS with backtracking because:
1. Explores paths character-by-character through grid
2. Must track visited cells within current path only (not globally)
3. Requires backtracking to "un-visit" cells when path fails
4. Combines grid traversal (Matrix) with decision exploration (Backtracking)

## Why DFS Works
**Invariant:** At each step, we've matched a prefix of the word; remaining characters must be found via adjacent unvisited cells.

**Strategy:**
- Try starting DFS from every cell matching word[0]
- At each step, check if current cell matches expected character
- Mark cell as visited (temporarily) to prevent reuse
- Explore all 4 directions for next character
- Backtrack: unmark cell if path fails, try other directions

## Python Solution
```python
def exist(board: list[list[str]], word: str) -> bool:
    """
    Check if word exists as path in grid using DFS backtracking.
    Time: O(m*n*4^L) where L = word length (worst case)
    Space: O(L) recursion depth
    """
    if not board or not board[0]:
        return False
    
    rows, cols = len(board), len(board[0])
    
    def dfs(r, c, index):
        """
        Check if word[index:] can be found starting from (r, c).
        """
        # Base case: entire word matched
        if index == len(word):
            return True
        
        # Boundary or mismatch check
        if (r < 0 or r >= rows or c < 0 or c >= cols or
            board[r][c] != word[index]):
            return False
        
        # Mark cell as visited (use sentinel to avoid extra set)
        temp = board[r][c]
        board[r][c] = '#'
        
        # Explore all 4 directions
        found = (dfs(r + 1, c, index + 1) or
                 dfs(r - 1, c, index + 1) or
                 dfs(r, c + 1, index + 1) or
                 dfs(r, c - 1, index + 1))
        
        # Backtrack: restore original character
        board[r][c] = temp
        
        return found
    
    # Try starting from every cell
    for r in range(rows):
        for c in range(cols):
            if dfs(r, c, 0):
                return True
    
    return False
```

## Reasoning
1. **Try All Starting Points:** Word could start anywhere matching first character
2. **Character Match Check:** Current cell must match expected word character
3. **Mark Visited:** Temporarily change cell value to prevent reuse in same path
4. **Explore Neighbors:** Try all 4 directions for next character in word
5. **Backtrack:** Restore original cell value after exploring (whether success or failure)
6. **Base Case:** If entire word matched (index reaches word length), return True

## Complexity Analysis
- **Time:** O(m·n·4^L)
  - m·n starting positions to try
  - 4^L worst-case branching factor (4 directions, L characters)
  - In practice much faster due to early termination on mismatch

- **Space:** O(L)
  - Recursion depth equals word length
  - No extra visited array (using sentinel value in-place)

## Edge Cases & Validation
```python
# Single character match
board = [["A"]]
assert exist(board, "A") == True
assert exist(board, "B") == False

# Simple path
board = [["A","B"],["C","D"]]
assert exist(board, "ABDC") == True  # A->B->D->C path
assert exist(board, "ABCD") == False  # No valid path

# Word longer than available cells
board = [["A","B"],["C","D"]]
assert exist(board, "ABCDE") == False  # Only 4 cells, 5 chars needed

# Reused cell attempt (should fail)
board = [["A","A"]]
assert exist(board, "AAA") == False  # Only 2 cells, can't reuse

# Classic LeetCode example
board = [
    ["A","B","C","E"],
    ["S","F","C","S"],
    ["A","D","E","E"]
]
assert exist(board, "ABCCED") == True
assert exist(board, "SEE") == True
assert exist(board, "ABCB") == False  # B reused
```

## Common Mistakes
1. **[[Missing Visited Set]]:** Not tracking visited cells within current path
   - Without marking, could revisit same cell (invalid reuse)

2. **Forgetting to Backtrack:** Not restoring cell value after exploring
   - Corrupts board state for other starting positions
   - Causes false negatives in subsequent searches

3. **[[Boundary Errors]]:** Missing bounds check before array access
   - Must check row/col bounds before indexing board[r][c]

4. **Global vs. Local Visited State:**
   - Visited must be per-path, not global (cell can be reused for different starting points)
   - In-place marking with backtrack solves this elegantly

5. **Wrong Base Case Position:**
   - Checking `index == len(word)` BEFORE boundary check
   - Order matters: base case first, then boundary, then mismatch

## Interview Walkthrough
**Interviewer:** "Find if word exists as a path in the character grid."

**Your Response:**
1. "I'll try starting DFS from every cell that matches the first character."
2. "At each step, I verify the current cell matches the expected character."
3. "To avoid reusing cells, I'll temporarily mark visited cells (using a sentinel value)."
4. "After exploring all directions, I backtrack—restore the cell—to try other paths."

**Why This Resonates:**
- Shows understanding of path-based backtracking (not just simple traversal)
- Explains why in-place marking is elegant (no extra space for visited set)
- Demonstrates proper backtrack ordering (restore after exploring)

**Common Follow-Ups:**
- "Can you find ALL words from a dictionary?" → Trie + DFS (Word Search II)
- "What if diagonal movement is allowed?" → Add 4 more directions
- "Optimize for very large word list?" → Build Trie first for efficient prefix checking

## Variations
1. **Word Search II:** Find all words from a list existing in grid (use Trie for efficiency)
2. **Word Break:** Similar backtracking but for string segmentation
3. **Sudoku Solver:** Backtracking with constraint checking (different domain, similar pattern)
4. **N-Queens:** Backtracking with positional constraints

## Related Problems
- [[DFS - Permutations]] (backtracking without grid constraints)
- [[DFS - Number of Islands]] (grid DFS without backtracking/reuse)
- [[Backtracking Representative Problems]]
- [[DFS Representative Problems]]

## Related Concepts
- [[DFS]] — core traversal mechanism
- [[Backtracking]] — undo-and-retry pattern
- [[Matrix Traversal]] — grid-specific navigation
- [[Trie]] — optimization for multiple word search


---
type: problem
status: stable
tags: [dsa/problem, dsa/trie, dsa/backtracking]
canonical: true
related: [[[Trie]], [[Backtracking]], [[DFS]]]
difficulty: hard
---
# Trie - Word Search II

## Problem Statement
Given an m×n grid of characters and a list of words, find all words that can be constructed from adjacent letters (horizontally/vertically), using each cell at most once per word.

**Constraints:**
- Multiple words to find simultaneously (unlike single Word Search)
- Same cell can't be reused within a single word's path

## Classification
- **Patterns:** [[Trie]], [[Backtracking]], [[DFS]]
- **Category:** Multi-pattern search optimization via prefix tree

## Pattern Selection
This is canonical for Trie because searching for multiple words independently (like calling Word Search for each word) would be inefficient. Building a Trie from all words allows a SINGLE grid traversal to find all matches, pruning branches early when no word has that prefix.

## Why Trie Works
**Key Insight:** Instead of checking "does this path match word X" for each word separately, build a Trie of ALL words. During DFS, only continue exploring if the current path is a valid PREFIX in the Trie—this naturally prunes impossible branches for ALL words simultaneously.

## Python Solution
```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.word = None  # Stores complete word at end nodes

def findWords(board: list[list[str]], words: list[str]) -> list[str]:
    """
    Find all words from list existing in grid using Trie + DFS.
    Time: O(m*n*4^L) worst case, heavily pruned by Trie
    Space: O(sum of word lengths) for Trie
    """
    # Build Trie from all words
    root = TrieNode()
    for word in words:
        node = root
        for ch in word:
            if ch not in node.children:
                node.children[ch] = TrieNode()
            node = node.children[ch]
        node.word = word
    
    rows, cols = len(board), len(board[0])
    result = []
    
    def dfs(r, c, node):
        ch = board[r][c]
        if ch not in node.children:
            return  # No word has this prefix, prune
        
        next_node = node.children[ch]
        if next_node.word:
            result.append(next_node.word)
            next_node.word = None  # Avoid duplicate additions
        
        board[r][c] = '#'  # Mark visited
        
        for dr, dc in [(-1,0),(1,0),(0,-1),(0,1)]:
            nr, nc = r + dr, c + dc
            if 0 <= nr < rows and 0 <= nc < cols and board[nr][nc] != '#':
                dfs(nr, nc, next_node)
        
        board[r][c] = ch  # Backtrack
    
    for r in range(rows):
        for c in range(cols):
            dfs(r, c, root)
    
    return result
```

## Reasoning
1. **Build Trie:** Insert all words into a shared Trie structure
2. **Single Grid Traversal:** DFS from each cell, following Trie structure instead of matching individual words
3. **Prune Early:** If current character isn't a valid Trie child, stop exploring this branch (invalid for ALL remaining words)
4. **Mark Found Words:** Set node.word = None after finding to avoid duplicate results
5. **Backtrack:** Restore board state after exploring each direction

## Complexity Analysis
- **Time:** O(m·n·4^L) worst case, but Trie pruning makes it much faster in practice (shared prefixes eliminate redundant exploration)
- **Space:** O(sum of all word lengths) for Trie storage

## Edge Cases & Validation
```python
board = [["o","a","a","n"],
         ["e","t","a","e"],
         ["i","h","k","r"],
         ["i","f","l","v"]]
words = ["oath","pea","eat","rain"]
result = findWords(board, words)
assert sorted(result) == sorted(["oath","eat"])
```

## Common Mistakes
1. **Searching Each Word Independently:** O(words × single_search) instead of shared Trie traversal
2. **Not Pruning Trie Branches:** Missing the early termination when character isn't a valid Trie child
3. **Duplicate Results:** Not clearing node.word after finding, causing same word added multiple times if multiple paths exist

## Interview Walkthrough
**Interviewer:** "Find all words from a list existing in this grid."

**Your Response:**
1. "Naive approach: run Word Search for each word separately—inefficient with many words."
2. "Instead, build a Trie from all words, enabling a SINGLE grid traversal."
3. "During DFS, only continue if current path is a valid prefix in the Trie—this prunes for ALL words simultaneously."
4. "When we reach a complete word marker, record it and clear the marker to avoid duplicates."

**Why This Resonates:**
- Shows the key insight of shared computation via Trie (not obvious without prior exposure)
- Explains the pruning benefit clearly (single bad character eliminates many word possibilities at once)

## Related Problems
- [[Backtracking - Word Search]] (single-word version, simpler backtracking)
- [[Trie - Implement Trie]] (foundational Trie operations)
- [[Trie Representative Problems]]

## Related Concepts
- [[Trie]] — shared prefix optimization
- [[Backtracking]] — path exploration with reuse constraint
- [[DFS]] — traversal mechanism


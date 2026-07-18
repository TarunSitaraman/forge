---
type: problem
status: stable
tags: [dsa/problem, dsa/dynamic-programming, dsa/string]
canonical: true
related: [[[Dynamic Programming]], [[Python - Prefix Sum]]]
difficulty: medium
---
# Dynamic Programming - Longest Common Subsequence

## Problem Statement
Given two strings text1 and text2, return the length of their longest common subsequence (LCS). A subsequence is derived by deleting some (or no) characters without changing relative order.

**Constraints:**
- Subsequence need not be contiguous (unlike substring)
- 1 ≤ text1.length, text2.length ≤ 1000
- Strings contain lowercase English letters

## Classification
- **Patterns:** [[Dynamic Programming]]
- **Category:** 2D DP for string comparison

## Pattern Selection
This is canonical for 2D DP because:
1. Optimal substructure: LCS(i,j) depends on LCS(i-1,j-1), LCS(i-1,j), LCS(i,j-1)
2. Overlapping subproblems: same (i,j) pairs recomputed in naive recursion
3. Classic string alignment problem template (extends to edit distance, etc.)

## Why DP Works
**State Definition:** dp[i][j] = length of LCS between text1[0:i] and text2[0:j]

**Transition Logic:**
- If characters match (text1[i-1] == text2[j-1]): dp[i][j] = dp[i-1][j-1] + 1
- If they don't match: dp[i][j] = max(dp[i-1][j], dp[i][j-1])

## Python Solution
```python
def longestCommonSubsequence(text1: str, text2: str) -> int:
    """
    Find LCS length using 2D dynamic programming.
    Time: O(m*n) where m, n are string lengths
    Space: O(m*n) for DP table
    """
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i-1] == text2[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    
    return dp[m][n]


def longestCommonSubsequenceOptimized(text1: str, text2: str) -> int:
    """
    Space-optimized version using rolling arrays.
    Time: O(m*n)
    Space: O(min(m,n)) - only keep 2 rows
    """
    if len(text1) < len(text2):
        text1, text2 = text2, text1  # Ensure text2 is shorter
    
    m, n = len(text1), len(text2)
    prev = [0] * (n + 1)
    
    for i in range(1, m + 1):
        curr = [0] * (n + 1)
        for j in range(1, n + 1):
            if text1[i-1] == text2[j-1]:
                curr[j] = prev[j-1] + 1
            else:
                curr[j] = max(prev[j], curr[j-1])
        prev = curr
    
    return prev[n]


def reconstructLCS(text1: str, text2: str) -> str:
    """
    Reconstruct actual LCS string, not just length.
    """
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i-1] == text2[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    
    # Backtrack to reconstruct
    result = []
    i, j = m, n
    while i > 0 and j > 0:
        if text1[i-1] == text2[j-1]:
            result.append(text1[i-1])
            i -= 1
            j -= 1
        elif dp[i-1][j] > dp[i][j-1]:
            i -= 1
        else:
            j -= 1
    
    return ''.join(reversed(result))
```

## Reasoning
1. **State Definition:** dp[i][j] represents LCS length using first i chars of text1, first j of text2
2. **Base Case:** dp[0][j] = dp[i][0] = 0 (empty string has no common subsequence)
3. **Transition:** Character match extends previous diagonal; mismatch takes best of skipping either character
4. **Fill Order:** Row by row (or column by column), since dp[i][j] depends on dp[i-1][*] and dp[*][j-1]
5. **Answer:** dp[m][n] gives final LCS length

## Complexity Analysis
- **Time:** O(m·n) — fill each cell once
- **Space:** 
  - Standard: O(m·n) for full DP table
  - Optimized: O(min(m,n)) using rolling array technique

## Edge Cases & Validation
```python
# No common subsequence
assert longestCommonSubsequence("abc", "def") == 0

# One string is subsequence of other
assert longestCommonSubsequence("abc", "aebdc") == 3  # "abc"

# Identical strings
assert longestCommonSubsequence("abc", "abc") == 3

# Empty string
assert longestCommonSubsequence("", "abc") == 0

# Classic example
assert longestCommonSubsequence("abcde", "ace") == 3  # "ace"

# Single character match
assert longestCommonSubsequence("a", "a") == 1
assert longestCommonSubsequence("a", "b") == 0
```

## Common Mistakes
1. **[[Off-by-One]]:** Confusing 0-indexed strings with 1-indexed DP array
   - `text1[i-1]` not `text1[i]` when using 1-indexed dp array

2. **[[Incorrect Base Case]]:** Not initializing dp[0][*] and dp[*][0] to 0

3. **Wrong Transition for Mismatch:**
   - Must take MAX of dp[i-1][j] and dp[i][j-1], not just one
   - Common bug: only considering one direction

4. **Confusing Substring with Subsequence:**
   - LCS allows gaps (non-contiguous)
   - Longest Common SUBSTRING requires contiguous match (different algorithm)

## Interview Walkthrough
**Interviewer:** "Find longest common subsequence between two strings."

**Your Response:**
1. "I'll define dp[i][j] as LCS length using first i chars of text1, first j of text2."
2. "If characters match, extend diagonal: dp[i][j] = dp[i-1][j-1] + 1."
3. "If they don't match, take best of skipping either character: max(dp[i-1][j], dp[i][j-1])."
4. "This achieves O(m*n) time, and I can optimize space to O(min(m,n)) using rolling arrays."

**Why This Resonates:**
- Shows clear state definition and transition reasoning
- Demonstrates space optimization awareness
- Distinguishes subsequence (LCS) from substring correctly

## Variations
1. **Longest Common Substring:** Requires contiguous match; different DP formulation (reset to 0 on mismatch)
2. **Edit Distance:** Similar 2D DP but tracks insert/delete/replace operations
3. **Shortest Common Supersequence:** Related problem combining both strings minimally
4. **Print LCS:** Reconstruct actual subsequence via backtracking (shown above)

## Related Problems
- [[Dynamic Programming - Climbing Stairs]] (simpler 1D DP for comparison)
- [[Dynamic Programming Representative Problems]]

## Related Concepts
- [[Dynamic Programming]] — 2D DP fundamental pattern
- [[String Matching]] — related string algorithms
- [[Recursion]] — naive recursive solution before DP optimization


---
type: problem
status: stable
tags: [dsa/problem, dsa/memoization, dsa/dynamic-programming]
canonical: true
related: [[[Memoization]], [[Dynamic Programming]]]
difficulty: medium
---
# Memoization - Word Break

## Problem Statement
Given a string and a dictionary of words, determine if the string can be segmented into a space-separated sequence of dictionary words.

**Constraints:**
- Words in dictionary may be reused multiple times
- 1 ≤ s.length ≤ 300

## Classification
- **Patterns:** [[Memoization]], [[Dynamic Programming]]
- **Category:** String segmentation with overlapping subproblems

## Pattern Selection
This is canonical for Memoization because naive recursion re-explores the same substring starting positions repeatedly, causing exponential blowup without caching.

## Why Memoization Works
**Key Insight:** canBreak(s[i:]) is computed multiple times via different paths in naive recursion. Caching by starting index eliminates redundant work.

## Python Solution
```python
def wordBreak(s: str, wordDict: list[str]) -> bool:
    """
    Check if string can be segmented using dictionary words.
    Time: O(n^2) with memoization (n = string length)
    Space: O(n) for cache + O(n) recursion depth
    """
    word_set = set(wordDict)
    memo = {}
    
    def canBreak(start):
        if start == len(s):
            return True  # Reached end successfully
        
        if start in memo:
            return memo[start]
        
        for end in range(start + 1, len(s) + 1):
            if s[start:end] in word_set and canBreak(end):
                memo[start] = True
                return True
        
        memo[start] = False
        return False
    
    return canBreak(0)
```

## Reasoning
1. **State Definition:** canBreak(i) = can s[i:] be segmented using dictionary words?
2. **Base Case:** canBreak(len(s)) = True (empty remainder is trivially segmentable)
3. **Transition:** Try all possible next word boundaries; if word matches AND rest is breakable, success
4. **Memoization:** Cache results by starting index to avoid recomputation

## Complexity Analysis
- **Time:** O(n²) — n possible starting positions, O(n) work checking substrings at each
- **Space:** O(n) — memoization cache + recursion depth

## Edge Cases & Validation
```python
assert wordBreak("leetcode", ["leet","code"]) == True
assert wordBreak("applepenapple", ["apple","pen"]) == True
assert wordBreak("catsandog", ["cats","dog","sand","and","cat"]) == False
assert wordBreak("", []) == True  # Empty string trivially breakable
```

## Common Mistakes
1. **No Memoization:** Naive recursion becomes exponential without caching
2. **Wrong Cache Key:** Must cache by starting index (integer), not substring (avoids redundant string creation)
3. **Not Checking All Word Boundaries:** Must try every possible end position, not just word lengths from dictionary

## Interview Walkthrough
**Interviewer:** "Can this string be segmented using dictionary words?"

**Your Response:**
1. "Naive recursion re-explores same starting positions repeatedly—I'll add memoization."
2. "For each starting index, try all possible word endings; if word matches and rest is breakable, success."
3. "Caching by starting index transforms exponential time to O(n²)."

## Related Problems
- [[Memoization Representative Problems]]

## Related Concepts
- [[Memoization]] — caching technique
- [[Dynamic Programming]] — bottom-up alternative formulation


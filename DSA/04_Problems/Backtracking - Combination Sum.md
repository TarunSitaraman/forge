---
type: problem
status: stable
tags: [dsa/problem, dsa/backtracking]
canonical: true
related: [[[Backtracking]], [[DFS]]]
difficulty: medium
---
# Backtracking - Combination Sum

## Problem Statement
Given an array of distinct positive integers and a target, find all unique combinations where chosen numbers sum to target. The same number may be chosen unlimited times.

**Constraints:**
- 1 ≤ candidates.length ≤ 30
- All elements distinct, positive integers
- Same number can be reused unlimited times
- Return combinations in any order (no duplicates)

## Classification
- **Patterns:** [[Backtracking]], [[DFS]]
- **Category:** Combination generation with reuse and sum constraint

## Pattern Selection
This is canonical for Backtracking with reuse because:
1. Elements can be reused unlimited times (differs from typical combination problems)
2. Sum constraint provides natural pruning (stop when sum exceeds target)
3. Avoiding duplicate combinations requires careful index management (not going backward)

## Why This Approach Works
**Key Insight - Avoiding Duplicates:**
- Don't allow "going backward" in candidate selection
- Once we've decided NOT to use index i, never reconsider it (only move forward)
- This naturally avoids [2,3] and [3,2] being counted as separate combinations

## Python Solution
```python
def combinationSum(candidates: list[int], target: int) -> list[list[int]]:
    """
    Find all combinations summing to target (reuse allowed).
    Time: O(N^(T/M)) where T=target, M=minimum candidate value
    Space: O(T/M) recursion depth
    """
    result = []
    current = []
    
    def backtrack(start, remaining):
        if remaining == 0:
            result.append(current[:])
            return
        
        if remaining < 0:
            return  # Pruning: exceeded target
        
        for i in range(start, len(candidates)):
            current.append(candidates[i])
            
            # Key: pass 'i' not 'i+1' to allow reuse of same element
            backtrack(i, remaining - candidates[i])
            
            current.pop()  # Backtrack
    
    backtrack(0, target)
    return result


def combinationSum2(candidates: list[int], target: int) -> list[list[int]]:
    """
    Variant: each number used ONCE, candidates may have duplicates.
    Requires sorting + skip-duplicate logic.
    """
    candidates.sort()
    result = []
    current = []
    
    def backtrack(start, remaining):
        if remaining == 0:
            result.append(current[:])
            return
        
        for i in range(start, len(candidates)):
            if candidates[i] > remaining:
                break  # Pruning: sorted, so all further are too large
            
            # Skip duplicates at same recursion level
            if i > start and candidates[i] == candidates[i-1]:
                continue
            
            current.append(candidates[i])
            backtrack(i + 1, remaining - candidates[i])  # i+1: no reuse
            current.pop()
    
    backtrack(0, target)
    return result
```

## Reasoning
1. **Choice:** At each step, choose a candidate (starting from current index to allow reuse)
2. **Constraint Check:** If remaining becomes negative, prune this branch
3. **Base Case:** If remaining equals 0, valid combination found
4. **Reuse Logic:** Pass same index `i` (not `i+1`) to allow reusing same element
5. **Backtrack:** Remove last added element, try next candidate

## Complexity Analysis
- **Time:** O(N^(T/M)) where N=candidates count, T=target, M=minimum candidate value (exponential but pruned)
- **Space:** O(T/M) for recursion depth in worst case

## Edge Cases & Validation
```python
# Simple case
result = combinationSum([2,3,6,7], 7)
assert sorted(result) == sorted([[2,2,3],[7]])

# Single candidate reused multiple times
result = combinationSum([2], 8)
assert result == [[2,2,2,2]]

# No valid combination
result = combinationSum([5], 3)
assert result == []

# Multiple valid combinations
result = combinationSum([2,3,5], 8)
expected = [[2,2,2,2],[2,3,3],[3,5]]
assert sorted(result) == sorted(expected)

# CombinationSum2 with duplicates in input
result = combinationSum2([10,1,2,7,6,1,5], 8)
expected = [[1,1,6],[1,2,5],[1,7],[2,6]]
assert sorted(result) == sorted(expected)
```

## Common Mistakes
1. **Wrong Index for Reuse:** Using `i+1` instead of `i` when reuse is allowed (breaks unlimited reuse)
2. **Missing Duplicate Skip Logic (Variant 2):** Not skipping same-value candidates at same level, causing duplicate combinations
3. **[[Mutable Defaults]]:** Appending reference instead of copy `current[:]`
4. **No Pruning:** Not checking remaining < 0 early, wasting exploration on invalid branches

## Interview Walkthrough
**Interviewer:** "Find combinations summing to target, elements reusable."

**Your Response:**
1. "I'll use backtracking, choosing candidates and tracking remaining target."
2. "Since elements are reusable, I pass the same index forward (not i+1) after choosing."
3. "Sum constraint provides natural pruning—stop exploring if remaining goes negative."
4. "This avoids duplicate combinations since I never go backward in candidate selection."

**Why This Resonates:**
- Shows the key insight about index management for reuse vs. no-reuse variants
- Explains the natural pruning from the sum constraint
- Demonstrates awareness of related duplicate-handling variant

## Variations
1. **Combination Sum II:** No reuse, input may have duplicates (shown above)
2. **Combination Sum III:** Fixed number of elements (k) summing to target
3. **Combination Sum IV:** Count permutations (order matters), different DP-style solution

## Related Problems
- [[Backtracking - N-Queens]] (different constraint satisfaction structure)
- [[Backtracking - Permutations]] (order matters, no reuse)
- [[Backtracking Representative Problems]]

## Related Concepts
- [[Backtracking]] — choose-explore-unchoose with reuse consideration
- [[DFS]] — recursive exploration mechanism


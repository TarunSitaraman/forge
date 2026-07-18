---
type: problem
status: stable
tags: [dsa/problem, dsa/dfs, dsa/backtracking]
canonical: true
related: [[[DFS]], [[Backtracking]], [[Python - Backtracking]]]
difficulty: medium
---
# DFS - Permutations

## Problem Statement
Given an array of distinct integers, return all possible permutations in any order.

**Constraints:**
- 1 ≤ nums.length ≤ 6
- All integers are distinct
- Return list of all n! permutations

## Classification
- **Patterns:** [[DFS]], [[Backtracking]]
- **Template:** [[Python - Backtracking]]
- **Category:** Decision tree exploration for permutation generation

## Pattern Selection
This is canonical for DFS/Backtracking because:
1. Each permutation represents one root-to-leaf path in decision tree
2. At each level, choose one unused element
3. Must backtrack (undo choice) to explore alternatives
4. State (current permutation, used elements) must be properly restored

## Why DFS/Backtracking Works
**Invariant:** At each recursion level, we've made k choices; remaining n-k elements are candidates for next position.

**Strategy:**
- Track current partial permutation and used elements
- At each step, try each unused element
- Recurse with element added to permutation
- Backtrack: remove element, mark as unused, try next candidate
- Base case: permutation length equals n → valid complete permutation

## Python Solution
```python
def permute(nums: list[int]) -> list[list[int]]:
    """
    Generate all permutations using backtracking DFS.
    Time: O(n! * n) - n! permutations, O(n) to copy each
    Space: O(n) recursion depth + O(n! * n) for results
    """
    result = []
    current = []
    used = [False] * len(nums)
    
    def backtrack():
        # Base case: permutation complete
        if len(current) == len(nums):
            result.append(current[:])  # Copy current state
            return
        
        # Try each unused element
        for i in range(len(nums)):
            if used[i]:
                continue
            
            # Choose
            current.append(nums[i])
            used[i] = True
            
            # Explore
            backtrack()
            
            # Un-choose (backtrack)
            current.pop()
            used[i] = False
    
    backtrack()
    return result


def permute_swap(nums: list[int]) -> list[list[int]]:
    """
    Alternative: in-place swapping approach.
    Time: O(n! * n)
    Space: O(n) recursion depth (no extra used array)
    """
    result = []
    
    def backtrack(start):
        if start == len(nums):
            result.append(nums[:])
            return
        
        for i in range(start, len(nums)):
            # Swap current position with candidate
            nums[start], nums[i] = nums[i], nums[start]
            
            backtrack(start + 1)
            
            # Swap back (undo)
            nums[start], nums[i] = nums[i], nums[start]
    
    backtrack(0)
    return result
```

## Reasoning
1. **State Tracking:** Maintain current partial permutation and used[] boolean array
2. **Base Case:** When current permutation length equals input length, save copy
3. **Choice:** Try each unused element at current position
4. **Explore:** Recursively build rest of permutation
5. **Backtrack:** Undo choice (remove from current, mark unused) to try next candidate
6. **Result:** Collect all valid complete permutations

## Complexity Analysis
- **Time:** O(n! · n)
  - n! total permutations
  - O(n) to copy each permutation into result
  
- **Space:** O(n) for recursion depth (excluding output)
  - Output itself: O(n! · n) to store all permutations

## Edge Cases & Validation
```python
# Single element
assert permute([1]) == [[1]]

# Two elements
result = permute([1, 2])
assert sorted(result) == sorted([[1, 2], [2, 1]])

# Three elements (6 permutations)
result = permute([1, 2, 3])
assert len(result) == 6
expected = [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
assert sorted(result) == sorted(expected)

# Negative numbers
result = permute([-1, 0, 1])
assert len(result) == 6

# Verify no duplicates in each permutation
for perm in permute([1, 2, 3, 4]):
    assert len(set(perm)) == len(perm)
    assert sorted(perm) == [1, 2, 3, 4]
```

## Common Mistakes
1. **[[Mutable Defaults]]:** Appending reference instead of copy
   - Wrong: `result.append(current)` — appends reference, gets mutated later
   - Correct: `result.append(current[:])` — copies current state

2. **Forgetting to Backtrack:** Not undoing state changes
   - Must pop from current list AND reset used flag
   - Missing either causes incorrect results

3. **[[Off-by-One]]:** Incorrect base case condition
   - Should check `len(current) == len(nums)`, not `len(current) == len(nums) - 1`

4. **Order of Operations:** Choose-Explore-Unchoose must be strict sequence
   - Choosing after exploring, or unchoosing before exploring, breaks logic

## Interview Walkthrough
**Interviewer:** "Generate all permutations of an array."

**Your Response:**
1. "I'll use backtracking: build permutation one element at a time."
2. "At each step, try each unused element as the next choice."
3. "When permutation reaches full length, save a copy to results."
4. "After exploring with one choice, backtrack—undo it, try the next."

**Why This Resonates:**
- Shows understanding of decision tree exploration
- Explains choose-explore-unchoose pattern explicitly
- Demonstrates awareness of copy vs. reference pitfall

**Common Follow-Ups:**
- "What if array has duplicates?" → Sort first, skip duplicate choices at same level
- "Can you do this without extra space for `used`?" → Swap-based approach
- "What about combinations instead?" → Don't reset the starting index (order doesn't matter)

## Variations
1. **Permutations II (with duplicates):** Sort input; skip if `nums[i] == nums[i-1] and not used[i-1]`
2. **Next Permutation:** Find lexicographically next permutation without generating all
3. **Kth Permutation:** Find kth permutation directly using factorial number system
4. **Permutations of Length K:** Stop backtracking early when length reaches K

## Related Problems
- [[Backtracking Representative Problems]]
- [[DFS - Path Sum]] (different backtracking context: tree vs. array)
- [[DFS Representative Problems]]

## Related Concepts
- [[Backtracking]] — the overarching pattern
- [[DFS]] — traversal mechanism for decision tree
- [[Recursion]] — fundamental recursive structure


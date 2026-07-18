---
type: problem
status: stable
tags: [dsa/problem, dsa/greedy]
canonical: true
related: [[[Greedy]], [[Dynamic Programming]]]
difficulty: medium
---
# Greedy - Jump Game

## Problem Statement
Given an array where each element represents the maximum jump length from that position, determine if you can reach the last index starting from index 0.

**Constraints:**
- 1 ≤ nums.length ≤ 10^4
- 0 ≤ nums[i] ≤ 10^5
- Array contains non-negative integers

## Classification
- **Patterns:** [[Greedy]]
- **Category:** Reachability via greedy range extension

## Pattern Selection
This is canonical for Greedy because:
1. Track maximum reachable index as we scan left to right
2. Never need to "look back" or reconsider previous decisions
3. If current position exceeds max reachable, immediately fail
4. Simpler and more efficient than DP (which would also work but O(n) space)

## Why Greedy Works
**Invariant:** max_reach represents the furthest index reachable using positions processed so far.

**Key Insight:**
- At each position i, if i > max_reach, we can never get past this gap
- Otherwise, update max_reach = max(max_reach, i + nums[i])
- If max_reach ever covers the last index, success is guaranteed

## Python Solution
```python
def canJump(nums: list[int]) -> bool:
    """
    Determine if last index reachable using greedy range extension.
    Time: O(n) single pass
    Space: O(1) only tracking max_reach
    """
    max_reach = 0
    
    for i in range(len(nums)):
        # If current position unreachable, fail immediately
        if i > max_reach:
            return False
        
        # Update furthest reachable position
        max_reach = max(max_reach, i + nums[i])
        
        # Early exit optimization: already can reach end
        if max_reach >= len(nums) - 1:
            return True
    
    return True


def jumpMinimum(nums: list[int]) -> int:
    """
    Related problem: minimum jumps to reach last index (assumes always possible).
    Greedy BFS-like level tracking.
    """
    if len(nums) <= 1:
        return 0
    
    jumps = 0
    current_end = 0  # End of current "jump level"
    farthest = 0
    
    for i in range(len(nums) - 1):
        farthest = max(farthest, i + nums[i])
        
        if i == current_end:
            jumps += 1
            current_end = farthest
            
            if current_end >= len(nums) - 1:
                break
    
    return jumps
```

## Reasoning
1. **Track Maximum Reach:** As we scan, maintain the furthest index reachable so far
2. **Feasibility Check:** If current index exceeds max_reach, we've hit an impassable gap
3. **Update Reach:** At each valid position, extend max_reach using current position's jump range
4. **Early Termination:** If max_reach already covers the end, return True immediately
5. **Related Problem (Min Jumps):** Track "levels" like BFS, incrementing jump count when current level exhausted

## Complexity Analysis
- **Time:** O(n) — single pass through array
- **Space:** O(1) — only tracking max_reach variable

## Edge Cases & Validation
```python
# Simple reachable case
assert canJump([2, 3, 1, 1, 4]) == True

# Blocked by zero
assert canJump([3, 2, 1, 0, 4]) == False

# Single element (trivially true)
assert canJump([0]) == True

# All zeros except first (must jump exactly right)
assert canJump([1, 0, 0, 0]) == False

# Large jump covers everything
assert canJump([5, 0, 0, 0, 0]) == True

# Minimum jumps
assert jumpMinimum([2, 3, 1, 1, 4]) == 2
assert jumpMinimum([2, 3, 0, 1, 4]) == 2
```

## Common Mistakes
1. **Not Checking Feasibility Early:** Missing the `if i > max_reach: return False` check
2. **Confusing with DP Solution:** While DP works, it's O(n) space vs. greedy's O(1)
3. **Wrong Update Order:** Must check feasibility BEFORE updating max_reach, not after
4. **Min-Jumps Confusion:** Different problem structure (level-based) than simple reachability

## Interview Walkthrough
**Interviewer:** "Can you reach the last index given jump lengths?"

**Your Response:**
1. "I'll greedily track the maximum reachable index as I scan left to right."
2. "If I ever reach a position beyond current max_reach, it's impossible to proceed."
3. "Otherwise, I extend max_reach using the current position's jump range."
4. "This is O(n) time, O(1) space—more efficient than a DP approach."

**Why This Resonates:**
- Shows preference for greedy over DP when problem structure allows
- Explains the elegant range-extension logic clearly
- Demonstrates awareness of related "minimum jumps" variant with different mechanics

## Variations
1. **Jump Game II (Minimum Jumps):** Find minimum number of jumps (shown above, BFS-like greedy)
2. **Jump Game III:** Can jump left or right by exact amount (different problem, BFS/DFS needed)
3. **Jump Game IV:** Multiple jump options including same-value teleportation (BFS required)

## Related Problems
- [[Greedy Representative Problems]]
- [[Dynamic Programming]] (alternative but less efficient approach)

## Related Concepts
- [[Greedy]] — range-extension without reconsideration
- [[Dynamic Programming]] — alternative approach with more space usage


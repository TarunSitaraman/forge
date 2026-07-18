---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/dynamic-programming]
canonical: true
related: [[[Dynamic Programming]], [[Coding Interviews]], [[Communicating Thought Process]]]
---
# Dynamic Programming Interview Guide

This guide prepares you to communicate and defend DP solutions in technical interviews.

## Pattern Recognition (30 seconds)

**Green Flags for DP:**
- "Optimal" (min/max) with overlapping subproblems
- "Count number of ways" to do something
- Recursive structure with repeated identical subcalls
- Problem can be broken into smaller versions of itself

**Yellow Flags:**
- "Optimal" but no overlapping subproblems → Greedy might work
- Simple recursion without repeated states → plain recursion suffices

**Red Flags (probably NOT DP):**
- Single greedy choice always optimal (interval scheduling)
- No optimal substructure (each choice independent)

## Communication Template

```
Interviewer: "Find minimum coins to make amount X."

You: "This has optimal substructure: minCoins(X) depends on 
minCoins(X - coin) for each coin denomination. Subproblems overlap 
(same amount reached via different coin combinations), so DP applies.

Strategy:
1. Define state: dp[i] = minimum coins to make amount i
2. Base case: dp[0] = 0
3. Transition: dp[i] = min(dp[i-coin] + 1) for each valid coin
4. Answer: dp[amount]"
```

## The Core Mechanics

```python
def dp_solution(n):
    # 1. Define state
    dp = [0] * (n + 1)
    
    # 2. Base case
    dp[0] = base_value
    
    # 3. Transition (bottom-up)
    for i in range(1, n + 1):
        dp[i] = compute_from_smaller_subproblems(dp, i)
    
    # 4. Answer
    return dp[n]
```

## Variations & Pressure Test

**Interviewer:** "Can you reduce space complexity?"

Your Response:
- "If dp[i] only depends on dp[i-1] (or few previous states), use rolling variables instead of full array."
- "Reduces O(n) space to O(1) or O(k) for k-dependency."

**Interviewer:** "Top-down or bottom-up—which do you prefer?"

Your Response:
- "Top-down (memoization) is more intuitive—write recursive solution, add cache."
- "Bottom-up (tabulation) avoids recursion overhead, often more space-efficient."
- "I'll start top-down for clarity, then convert to bottom-up if needed."

**Interviewer:** "Can you reconstruct the actual solution, not just the value?"

Your Response:
- "Track additional state (parent pointers or choices made) alongside dp values."
- "Backtrack from final state using stored choices."

## Common Mistakes

### Mistake 1: Wrong State Definition
```python
# ❌ WRONG - ambiguous, doesn't capture enough info
dp[i] = "something"

# ✓ CORRECT - precise definition
dp[i] = "minimum cost to reach position i using valid moves"
```

### Mistake 2: Missing Base Case
```python
# ❌ WRONG - dp[0] undefined, causes errors
dp = [0] * n
for i in range(1, n):
    dp[i] = dp[i-1] + something

# ✓ CORRECT - explicit base case
dp = [0] * n
dp[0] = base_value  # Explicitly set
for i in range(1, n):
    dp[i] = dp[i-1] + something
```

### Mistake 3: Off-by-One in Indices
```python
# ❌ WRONG - array out of bounds
dp[i] = dp[i-1] + dp[i-2]  # Fails when i < 2

# ✓ CORRECT - handle boundary explicitly
if i == 0: dp[i] = base0
elif i == 1: dp[i] = base1
else: dp[i] = dp[i-1] + dp[i-2]
```

## The Five-Step DP Framework

1. **Define State:** What does dp[i] represent?
2. **Base Case:** What is dp[0] (or smallest case)?
3. **Transition:** How to compute dp[i] from smaller subproblems?
4. **Order:** What order to fill dp array (usually increasing i)?
5. **Answer:** Where is the final answer (dp[n], max(dp), etc.)?

## Practice Problems By Category

### Category 1: 1D DP
- Climbing Stairs
- House Robber
- Coin Change

### Category 2: 2D Grid DP
- Unique Paths
- Minimum Path Sum
- Edit Distance

### Category 3: String DP
- Longest Common Subsequence
- Longest Palindromic Substring
- Word Break

### Category 4: Interval DP
- Burst Balloons
- Matrix Chain Multiplication

## The Checklist

- [ ] Defined state clearly (what does dp[i] mean?)
- [ ] Established correct base case(s)
- [ ] Derived transition formula with reasoning
- [ ] Verified fill order matches dependencies
- [ ] Confirmed space/time complexity
- [ ] Considered space optimization if applicable

## Related Interview Content
- [[Coding Interviews]]
- [[Communicating Thought Process]]
- [[Dynamic Programming]] — Pattern deep dive
- [[Dynamic Programming Representative Problems]] — Practice problems


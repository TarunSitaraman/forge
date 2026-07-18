---
type: problem
status: stable
tags: [dsa/problem, dsa/dynamic-programming]
canonical: true
related: [[[Dynamic Programming]], [[Greedy]]]
difficulty: medium
---
# Dynamic Programming - Coin Change

## Problem Statement
Given coin denominations and a target amount, find the minimum number of coins needed to make that amount. Return -1 if impossible. Assume unlimited supply of each coin denomination.

**Constraints:**
- 1 ≤ coins.length ≤ 12
- 0 ≤ amount ≤ 10^4
- Each coin can be used unlimited times (unbounded knapsack)

## Classification
- **Patterns:** [[Dynamic Programming]]
- **Category:** Unbounded knapsack variant, 1D DP

## Pattern Selection
This is canonical for unbounded knapsack DP because:
1. Each coin can be reused unlimited times (distinguishes from 0-1 knapsack)
2. Optimal substructure: minCoins(amount) depends on minCoins(amount - coin) for each coin
3. Classic counterexample showing greedy FAILS (e.g., coins=[1,3,4], amount=6: greedy picks 4+1+1=3 coins, optimal is 3+3=2 coins)

## Why DP Works (Not Greedy)
**Key Insight:** Unlike some coin systems (US currency), NOT all coin systems allow greedy to work correctly.

**Counter-Example Proving Greedy Fails:**
- Coins = [1, 3, 4], Amount = 6
- Greedy (largest first): 4 + 1 + 1 = 3 coins
- Optimal: 3 + 3 = 2 coins
- This proves we need DP, not just greedy selection

## Python Solution
```python
def coinChange(coins: list[int], amount: int) -> int:
    """
    Find minimum coins to make amount using bottom-up DP.
    Time: O(amount * len(coins))
    Space: O(amount)
    """
    # dp[i] = minimum coins to make amount i
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0  # Base case: 0 coins needed for amount 0
    
    for i in range(1, amount + 1):
        for coin in coins:
            if coin <= i:
                dp[i] = min(dp[i], dp[i - coin] + 1)
    
    return dp[amount] if dp[amount] != float('inf') else -1


def coinChangeWaysCount(coins: list[int], amount: int) -> int:
    """
    Related problem: count NUMBER OF WAYS to make amount (not minimum coins).
    Different DP formulation - order matters differently here.
    """
    dp = [0] * (amount + 1)
    dp[0] = 1  # One way to make amount 0: use no coins
    
    # Iterate coins in outer loop to avoid counting permutations as separate
    for coin in coins:
        for i in range(coin, amount + 1):
            dp[i] += dp[i - coin]
    
    return dp[amount]
```

## Reasoning
1. **State Definition:** dp[i] = minimum number of coins to make amount i
2. **Base Case:** dp[0] = 0 (zero coins needed for zero amount)
3. **Transition:** For each amount i, try every coin; if coin fits, check if using it improves the count
4. **Fill Order:** Increasing amount (dp[i] depends on dp[i-coin] where i-coin < i)
5. **Answer:** dp[amount], or -1 if still infinity (unreachable)

## Complexity Analysis
- **Time:** O(amount × len(coins)) — for each amount, try each coin
- **Space:** O(amount) — single 1D DP array

## Edge Cases & Validation
```python
# Simple case
assert coinChange([1, 3, 4], 6) == 2  # 3+3

# Impossible case
assert coinChange([2], 3) == -1  # Can't make odd amount with only even coin

# Amount is 0
assert coinChange([1, 2, 5], 0) == 0

# Single coin type
assert coinChange([1], 5) == 5  # Must use five 1-coins

# Classic US currency (greedy would also work here, but DP still correct)
assert coinChange([1, 5, 10, 25], 30) == 2  # 25+5

# Large amount with small coins
assert coinChange([1], 100) == 100

# Counting ways (different problem)
assert coinChangeWaysCount([1, 2, 5], 5) == 4  # Multiple ways to make 5
```

## Common Mistakes
1. **[[Incorrect Base Case]]:** Not setting dp[0] = 0 explicitly
2. **Using Greedy Instead of DP:** Assuming largest-coin-first always works (proven false above)
3. **Confusing Min-Coins with Count-Ways:** Different problems requiring different loop ordering
   - Min coins: coin loop INSIDE amount loop (order doesn't matter for counting minimum)
   - Count ways: coin loop OUTSIDE amount loop (prevents counting same combination as different permutations)
4. **Not Handling Impossible Case:** Forgetting to check for infinity/unreachable state

## Interview Walkthrough
**Interviewer:** "Find minimum coins for target amount."

**Your Response:**
1. "Greedy doesn't always work here—let me show a counter-example: coins=[1,3,4], amount=6."
2. "Greedy picks 4+1+1=3 coins, but optimal is 3+3=2 coins. This proves we need DP."
3. "I'll define dp[i] = minimum coins for amount i, building up from dp[0]=0."
4. "For each amount, I try every coin denomination and take the minimum."

**Why This Resonates:**
- Proactively addresses the "why not greedy" question with concrete counter-example
- Shows deep understanding of when DP is necessary vs. greedy sufficient
- Demonstrates awareness of related "count ways" variant with different loop ordering

## Variations
1. **Coin Change II (Count Ways):** Different problem - count combinations, not minimum (shown above)
2. **Coin Change with Limited Supply:** Becomes 0-1 knapsack instead of unbounded
3. **Minimum Coins with Specific Coins Used:** Track which coins were used, not just count
4. **Maximum Value with Weight Limit:** Classic knapsack variant

## Related Problems
- [[Dynamic Programming - Longest Common Subsequence]] (different DP structure: 2D vs 1D)
- [[Dynamic Programming - Climbing Stairs]] (similar 1D unbounded choice pattern)
- [[Dynamic Programming Representative Problems]]

## Related Concepts
- [[Dynamic Programming]] — unbounded knapsack pattern
- [[Greedy]] — why greedy fails here (important contrast)
- [[Recursion]] — naive recursive solution before DP optimization


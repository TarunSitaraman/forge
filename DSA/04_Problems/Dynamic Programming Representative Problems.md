---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/dynamic-programming]
canonical: true
---
# Dynamic Programming Representative Problems

Core problem variants that exercise Dynamic Programming across different recurrence structures and memo strategies.

## Variant 1: Unbounded Knapsack (Coin Change)
**Recognition:** Select items (unlimited) to optimize value subject to constraint (weight/cost).
**Mechanics:** DP[i] = optimization considering all possible last items that sum to i.
**Key Challenge:** Distinguishing order (permutations) vs. unordered (combinations).

- [[Dynamic Programming - Coin Change]]
- Variant: Minimum coins for amount
- Variant: Number of ways to make amount
- Variant: Combination sum IV (permutations count)

## Variant 2: Optimal Substructure (LIS, LCS)
**Recognition:** Longest/minimum subarray/subsequence satisfying property.
**Mechanics:** DP[i] = optimal value considering all ways to extend from j < i.
**Key Challenge:** Recurrence relation design; reconstruction of solution path.

- [[Dynamic Programming - Longest Increasing Subsequence]]
- Variant: Longest common subsequence (two strings)
- Variant: Edit distance (minimum operations)
- Variant: Longest substring (contiguous variant)

## Variant 3: 2D Grid Path Optimization
**Recognition:** Optimal path through 2D grid (min cost, max value, count paths).
**Mechanics:** DP[i][j] = optimal value reaching [i,j] from [0,0].
**Key Challenge:** Transitions (can move right/down only, or all directions?); obstacles.

- [[Dynamic Programming - Unique Paths]]
- Variant: Minimum path sum
- Variant: Maximum path sum with obstacles
- Variant: Count paths (combinatorial)

## Variant 4: String Matching (Distance, Patterns)
**Recognition:** Match, align, or transform strings with minimum edits/cost.
**Mechanics:** DP[i][j] = cost to transform/match s1[0:i] with s2[0:j].
**Key Challenge:** Insert/delete/replace operations; gap penalties.

- Variant: Edit distance
- Variant: Wildcard matching
- Variant: Regular expression matching
- Variant: Interleaving strings

## Variant 5: Interval/Range DP
**Recognition:** Optimize over all subarrays/intervals (often O(n³) complexity).
**Mechanics:** DP[i][j] = optimal value for subarray [i, j]; build from smaller intervals.
**Key Challenge:** Proper iteration order (length then start); transition logic.

- Variant: Burst balloons
- Variant: Matrix chain multiplication
- Variant: Palindrome partitioning
- Variant: Remove boxes

## Variant 6: State Compression (Bitmask DP)
**Recognition:** Small set of binary choices (n ≤ 20); use bitmask as DP state.
**Mechanics:** DP[mask] = optimal value with items/choices indicated by mask.
**Key Challenge:** Bitwise operations; O(2^n * n) complexity bounds.

- Variant: Traveling salesman (bitmask on visited cities)
- Variant: Subset selection with constraints
- Variant: Stock trading with constraints

## Cross-Linking
- **Pattern:** [[Dynamic Programming]]
- **Related:** [[Memoization]], [[Greedy]], [[Recursion]]
- **Templates:** [[Python - Prefix Sum]] (often used in DP)
- **Mistakes:** [[Incorrect Base Case]], [[Off-by-One]], [[Mutable Defaults]]
- **Interview Guide:** [[Dynamic Programming Interview Guide]]

## Key Mechanics
1. **Define State**: What does DP[i] (or DP[i][j]) represent?
2. **Base Case**: What is DP[0] or DP[1]?
3. **Transition**: How to compute DP[i] from smaller subproblems?
4. **Optimization**: Is there a greedy choice that eliminates subproblems?
5. **Reconstruction**: How to backtrack to find actual solution?

## Common Pitfalls
- Wrong base case (off by one, missing initialization)
- Incorrect recurrence (logic error in transition)
- State definition unclear (what does DP[i] mean?)
- Overlapping subproblems not recognized
- Over-optimizing (sometimes greedy choice breaks optimality)

## Complexity Patterns
- **1D Unbounded:** O(n * target) for coin change
- **2D LCS/Distance:** O(n * m) for string matching
- **2D Grid:** O(n²) or O(n³) depending on transition
- **Bitmask:** O(2^n * n) only viable for n ≤ 20


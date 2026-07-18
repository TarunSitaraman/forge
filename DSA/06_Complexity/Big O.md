---
type: complexity
status: stable
tags: [dsa/complexity, dsa/big-o]
canonical: true
related: [[[Time Complexity]], [[Space Complexity]], [[Amortized Analysis]]]
---
# Big O

## Concept
Asymptotic upper bound describing how resource use grows as input size increases.

## Reasoning Model
Use Big O to compare growth classes, not to predict exact runtime. Drop constants and lower-order terms only after identifying the measured variable.

## How To Analyze
1. Define the input variables precisely.
2. Identify the dominant operation or memory allocation.
3. Expand loops, recursive calls, and data-structure operations.
4. Simplify to the growth class only after preserving the relevant variables.
5. State assumptions such as graph density, hash distribution, key range, or recursion depth.

## Common Examples
- [[Binary Search]]: logarithmic search over monotonic space.
- [[DFS]] and [[BFS]]: linear graph traversal in V plus E.
- [[Merge Sort]]: divide-and-conquer recurrence with linear merge cost.
- [[Hash Map]]: expected constant lookup under normal hashing assumptions.

## Tradeoffs
Asymptotic improvement can hide constant factors, memory costs, and implementation risk. Prefer the simpler algorithm when constraints are small, but know the threshold where asymptotic behavior dominates.

## Edge Cases
- Multiple independent input dimensions
- Sparse versus dense graphs
- Worst-case hash collisions
- Recursion stack counted as auxiliary space
- Output-sensitive algorithms where result size dominates

## Interview Implications
Explain the measured variable before giving the bound. For graph problems, use V and E. For dynamic programming, count states and transition cost. For recursion, give both recurrence and simplified result when possible.

## Related Algorithms
- [[Time Complexity]], [[Space Complexity]], [[Amortized Analysis]]
- [[Algorithm Index]]

## Related Data Structures
- [[Array]]
- [[Hash Map]]
- [[Graph]]
- [[Tree]]

## Related Patterns
- [[Pattern Index]]


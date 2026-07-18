---
type: complexity
status: stable
tags: [dsa/complexity, dsa/graph-complexity]
canonical: true
related: [[[Graph]], [[DFS]], [[BFS]], [[Dijkstra]]]
---
# Graph Complexity

## Concept
Complexity measured in vertices V and edges E rather than a single n.

## Reasoning Model
Choose adjacency lists for sparse graphs and matrices when constant-time edge lookup or dense graphs dominate.

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
- [[Graph]], [[DFS]], [[BFS]], [[Dijkstra]]
- [[Algorithm Index]]

## Related Data Structures
- [[Array]]
- [[Hash Map]]
- [[Graph]]
- [[Tree]]

## Related Patterns
- [[Pattern Index]]


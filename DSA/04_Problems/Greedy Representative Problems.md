---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/greedy]
canonical: true
---
# Greedy Representative Problems

Core problem variants that exercise Greedy algorithms across different optimization domains where local choices yield global optimum.

## Variant 1: Interval Scheduling & Merging
**Recognition:** Select maximum non-overlapping intervals or merge overlapping ones.
**Mechanics:** Sort by end time; greedily select earliest-ending intervals.
**Key Challenge:** Why is earliest-end optimal? Proving greedy choice is safe.

- [[Greedy - Interval Scheduling]]
- Variant: Merge overlapping intervals
- Variant: Minimum meetings rooms (interval scheduling variant)
- Variant: Partition labels

## Variant 2: Activity Selection & Minimization
**Recognition:** Select activities/actions to optimize count or minimize cost.
**Mechanics:** Sort by relevant metric (end time, deadline, ratio); greedily pick best.
**Key Challenge:** Identifying correct sort order; proof that greedy choice works.

- Variant: Maximum activities
- Variant: Task scheduling with deadlines
- Variant: Gas station (jump game variant)

## Variant 3: Fractional/Integer Knapsack
**Recognition:** Select items (fractional allowed or integer) to maximize value within weight limit.
**Mechanics:** Sort by value/weight ratio; greedily take highest-ratio items.
**Key Challenge:** Fractional (greedy works) vs. 0-1 (DP needed); recognizing which.

- Variant: Fractional knapsack (continuous)
- Variant: Assign cookies (integer selection)
- Variant: Best time to buy/sell stock (greedy transactions)

## Variant 4: Lexicographic/Numeric Optimization
**Recognition:** Arrange elements to form smallest/largest number or lexicographically best result.
**Mechanics:** Define comparison; sort by that comparison; greedily construct result.
**Key Challenge:** Custom comparators; proving lexicographic choice is optimal.

- [[Greedy - Largest Number]]
- Variant: Smallest number after k swaps
- Variant: Create maximum number with k swaps
- Variant: Reorganize string

## Variant 5: Minimum Cost Selection
**Recognition:** Select items/edges to connect all/minimize total cost (MST, Huffman).
**Mechanics:** Greedily pick lowest-cost option that doesn't violate constraint.
**Key Challenge:** Distinguishing cases where greedy works (MST) vs. fails (TSP).

- Variant: Kruskal's algorithm (MST)
- Variant: Huffman coding (tree construction)
- Variant: Connect ropes (minimum cost)

## Variant 6: Problem-Specific Greedy
**Recognition:** Maximize profit, minimize moves, or optimize problem-specific metric.
**Mechanics:** Identify metric to optimize; design greedy choice; prove optimality.
**Key Challenge:** Proving greedy works (not all greedy problems have greedy solutions).

- Variant: Best time to buy/sell stock multiple times
- Variant: Candy distribution (greedy pass)
- Variant: Reconstructed itinerary (Eulerian path)

## Cross-Linking
- **Pattern:** [[Greedy]]
- **Related Patterns:** [[Dynamic Programming]] (often alternative to greedy)
- **Data Structures:** [[Heap]], [[Priority Queue]] (often used with greedy)
- **Mistakes:** [[Incorrect Base Case]], [[Off-by-One]]
- **Interview Guide:** [[Greedy Interview Guide]]

## Key Mechanics
1. **Identify Metric**: What are we optimizing (max/min)?
2. **Greedy Choice**: What choice looks best locally?
3. **Subproblem**: After choice, what remains to solve?
4. **Optimality Proof**: Why does greedy choice lead to global optimum?
5. **Termination**: When is solution complete?

## Common Pitfalls
- Greedy choice looks good but isn't optimal (prove first!)
- Wrong sort order (ascending vs. descending)
- Forgetting to check constraint (intervals overlap, weight exceeds, etc.)
- Off-by-one in comparisons
- Not recognizing when DP is needed (greedy fails)

## How to Recognize When Greedy Fails
- **Contradiction Example:** Find case where greedy choice leads to suboptimal result
- **Test Case:** Small examples often reveal when greedy fails
- **Proof Attempt:** Can't prove greedy choice is safe? → Probably fails

## Interview Red Flags
If you're tempted to use greedy but can't prove it works:
- ❌ Don't code the greedy solution
- ✅ Acknowledge uncertainty, suggest DP alternative or ask for proof

## Algorithms Based on Greedy
- Dijkstra (shortest path, single-source)
- Prim/Kruskal (minimum spanning tree)
- Huffman (optimal prefix-free code)
- Activity selection (interval scheduling)
- Coin change (if denominations form "canonical" system)


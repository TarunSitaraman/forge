---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/topological-sort]
canonical: true
---
# Topological Sort Representative Problems

Core problem variants that exercise Topological Sorting across different DAG ordering and dependency scenarios.

## Variant 1: Dependency Ordering (Kahn's Algorithm)
**Recognition:** Order tasks/courses by prerequisites (all dependencies before dependent).
**Mechanics:** Track in-degrees; process nodes with in-degree 0; remove edges as processing.
**Key Challenge:** Cycle detection (topological sort only works on DAGs); queue management.

- [[Topological Sort - Course Schedule]]
- Variant: Prerequisite order verification
- Variant: Build order validation
- Variant: Package dependency resolution

## Variant 2: DFS Post-Order Topological Sort
**Recognition:** Use DFS with post-order processing for topological ordering.
**Mechanics:** DFS from each unvisited node; add to result when children fully processed (post-order).
**Key Challenge:** Reverse the result (post-order gives reverse topological order).

- Variant: Using DFS instead of Kahn's
- Variant: Strongly connected components (Kosaraju uses post-order)
- Variant: Condensation graph (compress SCCs)

## Variant 3: Alien Dictionary (Ordering Constraint)
**Recognition:** Derive character ordering from sorted words with unknown alphabet.
**Mechanics:** Extract ordering constraints; run topological sort on character DAG.
**Key Challenge:** Building DAG from constraints; recognizing invalid inputs (contradictory).

- [[Topological Sort - Alien Dictionary]]
- Variant: Smallest equivalent string
- Variant: Verify alien dictionary

## Variant 4: Graph Coloring & Constraint Propagation
**Recognition:** Assign values/colors satisfying ordering constraints.
**Mechanics:** Topological order gives valid assignment order.
**Key Challenge:** Multiple valid topological orders; choosing lexicographically smallest.

- Variant: Smallest lexicographic order
- Variant: Number of topological sorts (combinatorial)

## Variant 5: Cycle Detection in DAG
**Recognition:** Verify graph is acyclic or detect cycles.
**Mechanics:** Failed topological sort (can't process all nodes) indicates cycle.
**Key Challenge:** Distinguishing back edges (cycle) from tree/forward edges.

- [[Topological Sort - Cycle Detection]]
- Variant: Find cycle path
- Variant: Cycle in directed graph (standard detection)

## Variant 6: Strongly Connected Components
**Recognition:** Find groups of nodes reachable from each other (SCC decomposition).
**Mechanics:** Kosaraju (two DFS passes) or Tarjan (single DFS with stack).
**Key Challenge:** Understanding reverse graph and SCC topology.

- [[Topological Sort - Strongly Connected Components]]
- Variant: Number of SCCs
- Variant: Condensation graph (compress to DAG of SCCs)

## Cross-Linking
- **Pattern:** [[Topological Sort]]
- **Related Patterns:** [[DFS]], [[BFS]], [[Graph Traversal]]
- **Algorithms:** [[Kosaraju]], [[Tarjan]]
- **Data Structures:** [[Graph]], [[Disjoint Set]]
- **Interview Guide:** [[Topological Sort Interview Guide]]

## When to Use Each Algorithm
| Algorithm | Time | Space | When |
|-----------|------|-------|------|
| Kahn's (BFS) | O(V+E) | O(V) | Preference or parallel processing |
| DFS Post-Order | O(V+E) | O(V) | Finding SCCs or reverse topo |
| Kosaraju | O(V+E) | O(V) | SCC decomposition |
| Tarjan | O(V+E) | O(V) | SCC decomposition (single pass) |

## Key Mechanics
1. **Prerequisites**: What must come before what?
2. **DAG Property**: Is graph acyclic? (required for topo sort)
3. **Processing Order**: What's a valid ordering?
4. **Constraints**: Topological sort vs. lexicographically smallest?
5. **Cycle Check**: How to detect if graph isn't a DAG?

## Common Pitfalls
- Assuming graph is acyclic (not always true; need to verify)
- Forgetting to handle nodes with in-degree 0 but no outgoing edges
- Modifying in-degrees without proper tracking
- Not initializing all nodes in topological sort
- Confusing topological order with lexicographic order

## Interview Red Flags
- "Find any topological order" → Kahn's or DFS, any valid answer
- "Find lexicographically smallest topological order" → Kahn's with priority queue
- "Detect cycle" → Failed topological sort or DFS back edge detection
- "Find SCCs" → Kosaraju or Tarjan


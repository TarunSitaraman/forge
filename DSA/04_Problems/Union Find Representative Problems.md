---
type: problem-pack
status: stable
tags: [dsa/problem, dsa-union-find]
canonical: true
---
# Union Find (Disjoint Set Union) Representative Problems

Core problem variants that exercise Union-Find across different connectivity, connectivity and dynamic graph scenarios.

## Variant 1: Connected Components Count
**Recognition:** Count number of connected components in graph.
**Mechanics:** Union all connected pairs; count number of distinct roots.
**Key Challenge:** Handling edge-based input vs. predefined components.

- [[Union Find - Number of Connected Components]]
- Variant: Largest connected component
- Variant: Connected components in matrix (grid)
- Variant: Friend circles (transitive closure)

## Variant 2: Cycle Detection in Undirected Graph
**Recognition:** Determine if undirected graph has cycle.
**Mechanics:** For each edge, check if both endpoints in same component; if yes → cycle found.
**Key Challenge:** Distinguishing from directed graph cycle detection.

- [[Union Find - Cycle Detection Undirected]]
- Variant: Detect cycle in directed graph (needs DFS)
- Variant: Redundant connection identification

## Variant 3: Minimum Spanning Tree (Kruskal's Algorithm)
**Recognition:** Find minimum-weight spanning tree connecting all nodes.
**Mechanics:** Sort edges by weight; use union-find to avoid cycles; add edges greedily.
**Key Challenge:** Proper use of union-find to avoid redundant edges; MST properties.

- [[Union Find - Minimum Spanning Tree]]
- Variant: Network delay time (shortest paths from start to all)
- Variant: Connecting cities with minimum cost

## Variant 4: Dynamic Connectivity Queries
**Recognition:** Process online queries for connectivity: "Are A and B connected?"
**Mechanics:** Union sets as connections form; query with find operation.
**Key Challenge:** Efficient find with path compression; union by rank.

- [[Union Find - Is Connected]]
- Variant: Number of provinces (connected components)
- Variant: Most stones removed (maximizing deletions via connectivity)

## Variant 5: Weighted Union Find (Potential Fields)
**Recognition:** Track relative weights/potentials between elements (e.g., "B is 5 higher than A").
**Mechanics:** Extend union-find to track relative values with path compression adjustments.
**Key Challenge:** Maintaining weight invariants during path compression.

- Variant: Evaluate equations (graph of ratios)
- Variant: Accounts merge (relative weights)
- Variant: Largest component relabel

## Variant 6: Offline Processing & Reconstruction
**Recognition:** Process queries offline by reconstructing graph in reverse (remove edges instead of add).
**Mechanics:** Process queries in reverse; use union-find for efficient batch processing.
**Key Challenge:** Recognizing problems suitable for offline approach; query ordering.

- Variant: Regions cut by slashes (process in reverse)
- Variant: Smallest subtree with all deepest nodes

## Cross-Linking
- **Pattern:** [[Union Find]]
- **Related Patterns:** [[DFS]], [[BFS]], [[Greedy]]
- **Algorithms:** [[Kruskal]] (MST via union-find)
- **Data Structure:** [[Disjoint Set]]
- **Interview Guide:** [[Union Find Interview Guide]]

## Key Operations
| Operation | Implementation | Time |
|-----------|---|---|
| **Make-Set(x)** | Create singleton set | O(1) |
| **Find(x)** | Get representative | O(α(n)) w/ compression |
| **Union(x,y)** | Merge sets | O(α(n)) w/ both optimizations |

Where α(n) is inverse Ackermann function (practically constant ≤ 4).

## Optimizations
1. **Path Compression**: During find, point all nodes directly to root
2. **Union by Rank**: Always attach smaller tree to larger tree

Both together achieve O(α(n)) amortized time per operation.

## Common Pitfalls
- Without path compression: O(n) per find (too slow)
- Without union by rank: Trees become chains (inefficient)
- Modifying parent/rank after initial setup (breaks invariants)
- Forgetting to initialize parent pointers (find returns wrong root)
- Confusing connected vs. merged in undirected graphs

## Interview Red Flags
- "Count components" → Union-find is likely best
- "Detect cycle" → Union-find for undirected, DFS for directed
- "Minimum spanning tree" → Kruskal with union-find
- "Online connectivity" → Union-find if queries only check find (not update)
- "Offline connectivity with updates" → Reverse processing with union-find

## When Union-Find Wins
- ✅ Static graph connectivity checks
- ✅ Connected components counting  
- ✅ Minimum spanning tree (Kruskal)
- ✅ Cycle detection in undirected graphs
- ✅ Batch processing of connectivity queries

## When Other Approaches Win
- ❌ Dynamic shortest paths → Dijkstra
- ❌ Longest paths → BFS/DFS
- ❌ Directed graph SCCs → Kosaraju/Tarjan
- ❌ Weighted paths with updates → Segment Tree


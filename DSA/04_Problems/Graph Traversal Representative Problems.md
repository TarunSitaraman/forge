---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/graph-traversal]
canonical: true
---
# Graph Traversal Representative Problems

Core problem variants that exercise Graph Traversal across different traversal objectives and graph types.

## Variant 1: Connected Components & Reachability
**Recognition:** Find all nodes reachable from a starting node or count connected components.
**Mechanics:** DFS/BFS from each unvisited node; mark visited; count number of root traversals.
**Key Challenge:** Distinguishing single-source reachability vs. all connected components.

- [[Graph Traversal - Number of Islands]]
- Variant: Reachability check (can reach B from A?)
- Variant: Connected components count
- Variant: Largest connected component

## Variant 2: Cycle Detection & DAG Validation
**Recognition:** Detect if graph contains cycle (directed or undirected).
**Mechanics:** DFS with color tracking (white/gray/black) or union-find for undirected.
**Key Challenge:** Distinguishing back edges (cycle) from other edge types.

- [[Graph Traversal - Cycle Detection]]
- Variant: Detect cycle in directed graph
- Variant: Validate DAG
- Variant: Find cycle path

## Variant 3: Path Finding & Distance Queries
**Recognition:** Find path between two nodes or compute distances.
**Mechanics:** BFS for unweighted (shortest path); DFS for path enumeration.
**Key Challenge:** Path reconstruction; handling unreachable nodes.

- [[Graph Traversal - Path Finding]]
- Variant: All paths from A to B
- Variant: Shortest path in unweighted graph
- Variant: Exists path with constraint

## Variant 4: Topological Ordering & Dependencies
**Recognition:** Order vertices respecting edge directions (pre-req before dependent).
**Mechanics:** DFS post-order or Kahn's algorithm (BFS with in-degrees).
**Key Challenge:** Cycle detection (topological sort only for DAGs); lexicographic ordering.

- [[Graph Traversal - Topological Order]]
- Variant: Detect cycle (failed topo sort)
- Variant: Lexicographically smallest order
- Variant: Alien dictionary (derive ordering from words)

## Variant 5: Strongly Connected Components (SCC)
**Recognition:** Find groups of mutually reachable nodes (directed graphs).
**Mechanics:** Kosaraju (reverse + DFS order) or Tarjan (single DFS with stack).
**Key Challenge:** Understanding reverse graph concept; SCC topology.

- [[Graph Traversal - Strongly Connected Components]]
- Variant: Number of SCCs
- Variant: Condensation graph (compress to DAG)
- Variant: SCC with specific properties

## Variant 6: Graph Coloring & Bipartiteness
**Recognition:** Check if graph is bipartite (2-colorable) or find valid coloring.
**Mechanics:** Color nodes with 2 colors via BFS/DFS; check no adjacent nodes have same color.
**Key Challenge:** Handling disconnected components; proving graph isn't bipartite.

- [[Graph Traversal - Bipartite Check]]
- Variant: Is valid coloring
- Variant: Odd-length cycle detection
- Variant: Maximum independent set (bipartite matching)

## Cross-Linking
- **Pattern:** [[Graph Traversal]]
- **Related Patterns:** [[DFS]], [[BFS]], [[Union Find]]
- **Algorithms:** [[Kosaraju]], [[Tarjan]], [[Topological Sort]]
- **Data Structures:** [[Graph]], [[Adjacency List]], [[Adjacency Matrix]]
- **Interview Guide:** [[Graph Traversal Interview Guide]]

## Graph Representation
| Type | Time | Space | When |
|---|---|---|---|
| **Adjacency List** | O(V+E) | O(V+E) | Sparse graphs (most common) |
| **Adjacency Matrix** | O(V²) | O(V²) | Dense graphs, quick edge lookup |
| **Edge List** | O(E) | O(E) | Sorting edges, MST algorithms |

## Traversal Comparison
| Approach | Best For | Space | Time |
|---|---|---|---|
| **DFS (Recursive)** | Paths, cycle detection | O(h) stack | O(V+E) |
| **DFS (Iterative)** | Same as recursive | O(V) stack | O(V+E) |
| **BFS** | Shortest path (unweighted) | O(V) queue | O(V+E) |

## Common Pitfalls
- Forgetting to mark nodes visited (infinite loops)
- Not handling disconnected components (only traverses one)
- Confusing undirected edges (should add both directions)
- Wrong cycle detection approach (directed vs. undirected differ)
- Not clearing visited set between separate traversals

## Interview Red Flags
- "Connected components" → DFS/BFS or union-find
- "Shortest path (unweighted)" → BFS
- "Detect cycle" → DFS for directed, union-find for undirected
- "Topological sort" → Kahn's or DFS post-order
- "SCC" → Kosaraju or Tarjan (advanced)
- "Bipartite" → 2-coloring via BFS/DFS

## When DFS/BFS Win
- ✅ Reachability queries
- ✅ Connected components
- ✅ Cycle detection
- ✅ Topological ordering
- ✅ Shortest path (unweighted)

## When Other Approaches Win
- ❌ Shortest path (weighted) → Dijkstra
- ❌ All-pairs shortest → Floyd-Warshall
- ❌ Minimum spanning tree → Kruskal/Prim
- ❌ Negative cycles → Bellman-Ford


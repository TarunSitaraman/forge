---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/bfs]
canonical: true
---
# BFS Representative Problems

Core problem variants that exercise Breadth-First Search across different layers, distances, and exploration objectives.

## Variant 1: Shortest Path in Unweighted Graph
**Recognition:** Find shortest path between nodes where all edges have unit weight.
**Mechanics:** Explore all neighbors at current distance before moving to next distance.
**Key Challenge:** Tracking parent pointers to reconstruct path; distinguishing visited from unvisited.

- [[BFS - Shortest Path Unweighted]]
- Variant: Shortest path exists check
- Variant: Path reconstruction (list of nodes)
- Variant: All shortest paths (multiple valid paths)

## Variant 2: Level-Order Traversal (Layers)
**Recognition:** Process graph or tree layer-by-layer (all nodes at distance k before distance k+1).
**Mechanics:** Queue-based exploration; process all queue items at each distance before next level.
**Key Challenge:** Distinguishing level boundaries; tracking which nodes belong to which level.

- [[BFS - Level Order Tree Traversal]]
- Variant: Binary tree level order
- Variant: N-ary tree level order
- Variant: Zigzag level order (alternate left-right)

## Variant 3: Connected Components & Reachability
**Recognition:** Count connected components or determine if all nodes are reachable.
**Mechanics:** BFS from each unvisited node counts as one component.
**Key Challenge:** Multiple disconnected components; recognizing when one BFS ends and next begins.

- [[BFS - Connected Components]]
- Variant: Largest connected component
- Variant: Bipartite graph (2-colorable)
- Variant: Diameter of graph (furthest pair)

## Variant 4: Topological Sort (Level-Based)
**Recognition:** Order vertices by dependencies on DAG using level-based approach (Kahn's algorithm).
**Mechanics:** Process nodes in-degree 0 level by level; remove edges as nodes process.
**Key Challenge:** Tracking in-degrees; recognizing when a node's dependencies are satisfied.

- [[BFS - Topological Sort Kahn]]
- Variant: Course prerequisites (dependency ordering)
- Variant: Alien dictionary (ordering by character)

## Variant 5: Distance & Multi-Source Exploration
**Recognition:** Find distance from source to all nodes, or distance from ANY of multiple sources.
**Mechanics:** Single-source: BFS from one start. Multi-source: Start all sources in queue simultaneously.
**Key Challenge:** Managing queue with multiple sources; distinguishing distance from each source.

- [[BFS - Multi-Source Distance]]
- Variant: Rotting oranges (multi-source, time-based)
- Variant: 0-1 BFS (two-weight edges using deque)
- Variant: Farthest cell (distance from all walls)

## Variant 6: Grid Traversal with Obstacles
**Recognition:** Navigate 2D grid from start to end avoiding obstacles.
**Mechanics:** BFS with 4-directional movement; track visited cells to avoid cycles.
**Key Challenge:** 2D coordinate management; boundary checking; obstacle handling.

- [[BFS - Grid Shortest Path]]
- Variant: Knight's minimum moves (8-directional)
- Variant: Maze with walls
- Variant: Sliding puzzle (state-space BFS)

## Cross-Linking
- **Pattern:** [[BFS]]
- **Related Pattern:** [[DFS]] (depth-first alternative), [[Tree Traversal]]
- **Template:** [[Python - BFS]]
- **Mistakes:** [[Missing Visited Set]], [[Infinite Loop]]
- **Cheat Sheet:** [[BFS Cheat Sheet]]
- **Interview Guide:** [[BFS Interview Guide]]

## Interview Insights
- **Recognition:** "Shortest path? Layers? Distance important? → BFS"
- **Common Ask:** "Shortest path in unweighted graph" or "all reachable nodes" or "level-order traversal"
- **Variant Pressure:** Multi-source BFS, 0-1 BFS, obstacle handling, distance tracking
- **Proof Question:** "Why BFS and not DFS for shortest path? What breaks with DFS?"
- **Optimization:** When to use bidirectional BFS? When to use deque for 0-1 BFS?

## Key Mechanics to Master
1. **Queue Operations:** FIFO ordering is essential (not stack)
2. **Distance Tracking:** Distance[node] = distance[parent] + 1
3. **Visited Set:** Prevent revisiting (avoids infinite loops and duplicate processing)
4. **Level Processing:** All nodes at distance k before distance k+1
5. **Multi-Source:** Multiple starting points in initial queue

## Time Complexity Pattern
Most BFS solutions achieve **O(V+E)** because:
- Visit each vertex once (V)
- Traverse each edge once (E)
- Total: O(V+E)

Avoid: Re-queuing already-visited nodes (doubles processing).

## Common Pitfalls
1. **Using Stack Instead of Queue:** Breaks level-order property
2. **Missing Visited Set:** Revisit nodes, infinite loops, exponential complexity
3. **Not Tracking Distance:** Can't answer "what is distance?" queries
4. **Boundary Errors in Grid:** Off-by-one in row/column checks
5. **Wrong Graph Representation:** Forgetting bidirectional edges in undirected graph

## Correctness Checklist
- [ ] Use Queue, not Stack or List
- [ ] Mark nodes visited WHEN ADDING to queue (not when processing)
- [ ] Handle all 4 directions (or 8 for knight/etc.)
- [ ] Check boundaries before accessing grid[r][c]
- [ ] Initialize distance correctly (0 for start, or infinity for others)
- [ ] Return answer correctly positioned (after BFS completes)

## Related Patterns
- [[DFS]]: Depth-first alternative; often slower for shortest path
- [[Topological Sort]]: BFS variant for DAG ordering
- [[Tree Traversal]]: BFS on acyclic structures
- [[Dijkstra]]: Generalization for weighted graphs

## When BFS Wins
- ✅ Shortest path in unweighted graph
- ✅ All nodes at exact distance k
- ✅ Minimum steps/moves
- ✅ Connected components
- ✅ Bipartite checking

## When Other Approaches Win
- ❌ Shortest path in weighted graph → Dijkstra
- ❌ Minimum spanning tree → Kruskal/Prim
- ❌ Topological sort required → DFS post-order often cleaner
- ❌ Memory critical → DFS uses less space


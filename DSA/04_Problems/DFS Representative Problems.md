---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/dfs]
canonical: true
---
# DFS Representative Problems

Core problem variants that exercise Depth-First Search across different structural scenarios and search objectives.

## Variant 1: Graph Reachability & Connected Components
**Recognition:** Determine reachability or find connected components in undirected/directed graphs.
**Mechanics:** DFS from node; mark visited; explore all neighbors recursively before backtracking.
**Key Challenge:** Distinguish single reachability check vs. finding all connected components (multiple DFS passes).

- [[DFS - Number of Islands]]
- Variant: Connected components in undirected graph
- Variant: Reachability in directed graph (can reach B from A?)
- Variant: Strongly connected components (Kosaraju's algorithm)

## Variant 2: Cycle Detection
**Recognition:** Detect cycles in directed or undirected graphs.
**Mechanics:** Track visited AND recursion stack states (for directed) or parent (for undirected).
**Key Challenge:** Distinguishing between back edges (cycle) and cross edges (no cycle).

- [[DFS - Cycle Detection]]
- Variant: Undirected graph cycle
- Variant: Directed graph cycle
- Variant: Topological ordering (if no cycle exists)

## Variant 3: Tree Traversal & Path Exploration
**Recognition:** Search within tree structures or explore all paths to leaves/targets.
**Mechanics:** Recursive exploration with state tracking; backtrack to explore alternatives.
**Key Challenge:** Maintaining path state and restoring during backtracking.

- [[DFS - Paths in Tree]]
- Variant: All root-to-leaf paths
- Variant: Paths with sum constraint
- Variant: Path with specific property

## Variant 4: Permutation & Combination Generation
**Recognition:** Generate all permutations, combinations, or partitions.
**Mechanics:** Build solution incrementally; backtrack when constraint violated.
**Key Challenge:** Avoiding duplicates; efficiently pruning impossible states.

- Variant: Permutations (order matters)
- Variant: Combinations (order doesn't matter)
- Variant: Subsets
- Variant: Partitions with constraints

## Variant 5: Topological Ordering & DAG Processing
**Recognition:** Order vertices of directed acyclic graph (DAG) by dependencies.
**Mechanics:** DFS with post-order processing; add to result when all children explored.
**Key Challenge:** Distinguishing topological order from DFS order.

- Variant: Topological sort on DAG
- Variant: Detect cycle (not a DAG)
- Variant: Kahn's algorithm (alternative BFS approach)

## Variant 6: Recursive Backtracking with Decision Trees
**Recognition:** Solve constraint satisfaction or optimization via exploring decision trees.
**Mechanics:** Decide → explore → undo (backtrack) → try next decision.
**Key Challenge:** Pruning branches; managing decision history.

- Variant: N-Queens problem
- Variant: Sudoku solver
- Variant: Word search in 2D grid
- Variant: Letter combinations of phone number

## Cross-Linking
- **Pattern:** [[DFS]]
- **Related Pattern:** [[BFS]] (breadth-first alternative), [[Backtracking]]
- **Template:** [[Python - DFS Recursive]], [[Python - DFS Iterative]]
- **Mistakes:** [[Missing Visited Set]], [[Recursion Depth]], [[Infinite Loop]]
- **Cheat Sheet:** [[DFS Cheat Sheet]]
- **Interview Guide:** [[DFS Interview Guide]]

## Interview Insights
- **Recognition:** "Explore all nodes? Need visited set? Path matters? → DFS"
- **Common Ask:** "Find all connected components" or "detect cycle" or "generate all solutions"
- **Variant Pressure:** Stack overflow (deep recursion), duplicate results, cycle confusion
- **Proof Question:** "Why must you track visited set? What happens without it?"
- **Optimization:** When to use iterative vs. recursive? When to use BFS instead?

## Key Mechanics to Master
1. **Visited Set:** Prevent revisiting nodes (avoid infinite loops)
2. **Recursion Stack:** Call stack IS the data structure (vs. explicit stack for iterative)
3. **Backtracking:** Undo state changes after exploring (for path-based problems)
4. **Discovery Order:** Track when nodes first entered vs. fully explored
5. **Termination:** Base case is reaching leaf or already-visited node

## Time Complexity Pattern
Most DFS solutions achieve **O(V+E)** because:
- Visit each vertex once (V)
- Traverse each edge once (E)
- Total: O(V+E)

Avoid: O(2^V) by exploring all subsets without pruning.

## Common Pitfalls
1. **Missing Visited Set:** Infinite loops on cycles
2. **Backtracking Error:** State not properly restored
3. **Recursion Depth:** Stack overflow on deep graphs
4. **Duplicate Results:** Not deduplicating permutations/combinations
5. **Wrong Termination:** Confusing "node visited" with "node processed"

## Related Patterns
- [[BFS]]: Breadth-first alternative with queue instead of recursion
- [[Backtracking]]: Specialized DFS for constraint satisfaction
- [[Tree Traversal]]: DFS on tree structures (no cycles)
- [[Graph Traversal]]: General graph exploration


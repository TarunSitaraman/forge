---
type: problem
status: stable
tags: [dsa/problem, dsa/bfs, dsa/graph]
canonical: true
related: [[[BFS]], [[Graph Traversal]], [[Python - BFS]]]
difficulty: medium
---
# BFS - Shortest Path Unweighted

## Problem Statement
Given an unweighted graph (adjacency list) and a source node, find the shortest distance from source to all other reachable nodes.

**Constraints:**
- Graph may be directed or undirected
- All edges have equal weight (unweighted)
- Some nodes may be unreachable (distance = -1 or infinity)

## Classification
- **Patterns:** [[BFS]], [[Graph Traversal]]
- **Template:** [[Python - BFS]]
- **Category:** Single-source shortest path in unweighted graph

## Pattern Selection
This is the canonical BFS application because:
1. BFS explores nodes in order of increasing distance from source
2. First time a node is visited, that's guaranteed to be shortest distance
3. Queue's FIFO property ensures layer-by-layer exploration
4. Unlike DFS, BFS naturally finds shortest paths in unweighted graphs

## Why BFS Works (Not DFS)
**Invariant:** All nodes at distance d are processed before any node at distance d+1.

**Why this matters:**
- When we first reach a node via BFS, no shorter path exists (would have been found already)
- DFS could reach a node via a long path first, giving wrong distance
- BFS guarantees optimal (shortest) first-discovery for unweighted graphs

## Python Solution
```python
from collections import deque

def shortestPath(graph: dict, source: int, n: int) -> list[int]:
    """
    Find shortest distance from source to all nodes.
    Time: O(V+E) each node/edge processed once
    Space: O(V) for distance array and queue
    """
    # Initialize distances to infinity (unreachable)
    distance = [-1] * n
    distance[source] = 0
    
    queue = deque([source])
    
    while queue:
        node = queue.popleft()
        
        for neighbor in graph.get(node, []):
            # First time visiting this neighbor
            if distance[neighbor] == -1:
                distance[neighbor] = distance[node] + 1
                queue.append(neighbor)
    
    return distance


def shortestPathWithParent(graph: dict, source: int, n: int) -> tuple:
    """
    Find shortest distances AND reconstruct paths.
    Returns (distances, parent_pointers)
    """
    distance = [-1] * n
    parent = [-1] * n
    distance[source] = 0
    
    queue = deque([source])
    
    while queue:
        node = queue.popleft()
        
        for neighbor in graph.get(node, []):
            if distance[neighbor] == -1:
                distance[neighbor] = distance[node] + 1
                parent[neighbor] = node
                queue.append(neighbor)
    
    return distance, parent


def reconstructPath(parent: list[int], source: int, target: int) -> list[int]:
    """Reconstruct actual path from parent pointers."""
    if parent[target] == -1 and target != source:
        return []  # No path exists
    
    path = []
    node = target
    while node != -1:
        path.append(node)
        node = parent[node]
    
    path.reverse()
    return path
```

## Reasoning
1. **Initialize:** Distance array with -1 (unreachable), source distance = 0
2. **Queue Setup:** Start with source node in queue
3. **Process Level by Level:** Pop node, examine all neighbors
4. **First Discovery:** If neighbor's distance still -1, this is shortest path
5. **Update & Enqueue:** Set distance, add to queue for further exploration
6. **Termination:** Queue empties when all reachable nodes processed

## Complexity Analysis
- **Time:** O(V+E)
  - Each vertex dequeued once: V
  - Each edge examined once: E
  - Total: V+E

- **Space:** O(V)
  - Distance array: O(V)
  - Queue: O(V) worst case (all nodes at same level)

## Edge Cases & Validation
```python
# Simple linear graph
graph = {0: [1], 1: [2], 2: [3]}
assert shortestPath(graph, 0, 4) == [0, 1, 2, 3]

# Disconnected graph
graph = {0: [1], 2: [3]}
result = shortestPath(graph, 0, 4)
assert result[0] == 0 and result[1] == 1
assert result[2] == -1 and result[3] == -1  # Unreachable

# Single node
graph = {0: []}
assert shortestPath(graph, 0, 1) == [0]

# Cyclic graph (BFS handles cycles correctly)
graph = {0: [1], 1: [2], 2: [0, 3]}
assert shortestPath(graph, 0, 4) == [0, 1, 2, 3]

# Multiple paths, shortest wins
graph = {0: [1, 2], 1: [3], 2: [3]}
result = shortestPath(graph, 0, 4)
assert result[3] == 2  # Via either 1 or 2, both length 2

# Path reconstruction
graph = {0: [1], 1: [2], 2: [3]}
dist, parent = shortestPathWithParent(graph, 0, 4)
path = reconstructPath(parent, 0, 3)
assert path == [0, 1, 2, 3]
```

## Common Mistakes
1. **[[Missing Visited Set]]:** Not checking distance before adding to queue
   - Without this check, nodes get added multiple times
   - Causes incorrect distances and infinite processing

2. **Using Stack Instead of Queue:** 
   - Stack gives DFS order, breaks shortest-path guarantee
   - Must use deque with FIFO operations (popleft, append)

3. **[[Boundary Errors]]:** Marking visited at wrong time
   - Correct: Mark when ADDING to queue (prevents duplicate additions)
   - Wrong: Mark when PROCESSING (allows duplicate additions before processing)

4. **Forgetting Disconnected Components:**
   - If graph has multiple components, only source's component gets distances
   - This is expected behavior—not a bug, but must be understood

5. **Distance Off-By-One:**
   - `distance[neighbor] = distance[node] + 1`, not just `distance[node]`

## Interview Walkthrough
**Interviewer:** "Find shortest path from source to all nodes in unweighted graph."

**Your Response:**
1. "BFS explores nodes layer by layer—all distance-1 nodes before distance-2."
2. "First time I reach a node, that's guaranteed to be the shortest distance."
3. "I use a queue (FIFO) to maintain this layer-by-layer property."
4. "I mark distance when adding to queue, not when processing, to avoid duplicates."

**Why This Resonates:**
- Explains WHY BFS gives shortest path (not just HOW)
- Contrasts with DFS to show understanding of tradeoffs
- Demonstrates the critical detail of when to mark visited

## Variations
1. **Bidirectional BFS:** Search from both source and target simultaneously (faster for large graphs)
2. **0-1 BFS:** Edges with weight 0 or 1; use deque instead of queue
3. **Multi-Source BFS:** Start with multiple sources in initial queue
4. **Weighted Shortest Path:** Requires Dijkstra's algorithm instead

## Related Problems
- [[BFS - Level Order Tree Traversal]] (similar layer-by-layer processing)
- [[BFS - Multi-Source Distance]] (extension to multiple starting points)
- [[BFS Representative Problems]]

## Related Concepts
- [[BFS]] — fundamental pattern
- [[Graph Traversal]] — broader context
- [[Dijkstra]] — generalization for weighted graphs
- [[Queue]] — essential data structure for BFS


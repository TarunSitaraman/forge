---
type: problem
status: stable
tags: [dsa/problem, dsa/graph-traversal]
canonical: true
related: [[[Graph Traversal]], [[BFS]], [[DFS]]]
difficulty: medium
---
# Graph Traversal - Bipartite Check

## Problem Statement
Given an undirected graph, determine if it is bipartite — can nodes be divided into two sets such that every edge connects nodes from different sets (2-colorable).

**Constraints:**
- Graph may be disconnected (multiple components)
- Graph given as adjacency list
- Must handle self-loops and multi-edges gracefully

## Classification
- **Patterns:** [[Graph Traversal]], [[BFS]], [[DFS]]
- **Category:** Graph coloring via traversal

## Pattern Selection
This is canonical for graph traversal because:
1. 2-coloring can be verified via BFS or DFS
2. Start with any color for source node, alternate for neighbors
3. If we ever find adjacent nodes with same color, graph is NOT bipartite
4. Must check all disconnected components independently

## Why This Approach Works
**Invariant:** If graph is bipartite, a valid 2-coloring exists where adjacent nodes always differ in color.

**Detection Strategy:**
- BFS/DFS assigns alternating colors level by level (or via recursion)
- Any edge connecting same-colored nodes proves non-bipartite
- Odd-length cycles are the mathematical reason bipartite fails (must detect this implicitly via coloring)

## Python Solution
```python
from collections import deque

def isBipartite(graph: list[list[int]]) -> bool:
    """
    Check if graph is bipartite using BFS 2-coloring.
    Time: O(V+E) visit each node/edge once
    Space: O(V) for color array and queue
    """
    n = len(graph)
    color = [0] * n  # 0 = uncolored, 1 = color A, -1 = color B
    
    for start in range(n):
        if color[start] != 0:
            continue  # Already colored (part of processed component)
        
        # BFS from this component's start
        color[start] = 1
        queue = deque([start])
        
        while queue:
            node = queue.popleft()
            
            for neighbor in graph[node]:
                if color[neighbor] == 0:
                    # Uncolored: assign opposite color
                    color[neighbor] = -color[node]
                    queue.append(neighbor)
                elif color[neighbor] == color[node]:
                    # Same color as current node: NOT bipartite
                    return False
    
    return True


def isBipartiteDFS(graph: list[list[int]]) -> bool:
    """
    Alternative: DFS-based 2-coloring.
    """
    n = len(graph)
    color = [0] * n
    
    def dfs(node, c):
        color[node] = c
        for neighbor in graph[node]:
            if color[neighbor] == c:
                return False
            if color[neighbor] == 0 and not dfs(neighbor, -c):
                return False
        return True
    
    for i in range(n):
        if color[i] == 0:
            if not dfs(i, 1):
                return False
    
    return True
```

## Reasoning
1. **Initialize Colors:** All nodes start uncolored (0)
2. **Process Each Component:** For disconnected graphs, must check every component
3. **BFS/DFS 2-Coloring:** Assign color 1 to start, then alternate (-1, 1, -1...) for neighbors
4. **Conflict Detection:** If neighbor already has SAME color as current node, bipartite fails
5. **Complete Check:** Must verify ALL components (single non-bipartite component fails entire graph)

## Complexity Analysis
- **Time:** O(V+E) — each vertex and edge examined once
- **Space:** O(V) — color array and BFS queue

## Edge Cases & Validation
```python
# Simple bipartite graph (even cycle)
graph = [[1,3],[0,2],[1,3],[0,2]]
assert isBipartite(graph) == True

# Odd cycle (NOT bipartite)
graph = [[1,2],[0,2],[0,1]]
assert isBipartite(graph) == False

# Disconnected graph, all components bipartite
graph = [[1],[0],[3],[2]]
assert isBipartite(graph) == True

# Disconnected graph, one component fails
graph = [[1],[0],[3,4],[2,4],[2,3]]  # Triangle in second component
assert isBipartite(graph) == False

# Single node (trivially bipartite)
graph = [[]]
assert isBipartite(graph) == True

# No edges at all
graph = [[],[],[]]
assert isBipartite(graph) == True
```

## Common Mistakes
1. **[[Missing Visited Set]]:** Not checking all components in disconnected graphs
2. **Wrong Color Comparison:** Checking `color[neighbor] != color[node]` for conflict instead of `==`
3. **Not Handling Isolated Nodes:** Nodes with no edges are trivially bipartite (single color fine)
4. **Confusing with General K-Coloring:** Bipartite specifically means 2-coloring; different from general graph coloring problems

## Interview Walkthrough
**Interviewer:** "Determine if this graph is bipartite."

**Your Response:**
1. "I'll use BFS/DFS to attempt a 2-coloring of the graph."
2. "Starting from any uncolored node, I alternate colors for neighbors."
3. "If I ever find two adjacent nodes with the SAME color, the graph isn't bipartite."
4. "I must check all disconnected components since one bad component fails the whole graph."

**Why This Resonates:**
- Shows connection between bipartiteness and 2-colorability
- Explains the conflict detection clearly (same color = failure)
- Demonstrates awareness of disconnected component handling

## Variations
1. **Possible Bipartition (Same Problem, Different Framing):** Identical algorithm, different problem statement
2. **Maximum Bipartite Matching:** Different problem entirely (requires augmenting paths)
3. **K-Colorable Graph:** General case is NP-hard; bipartite (k=2) is special polynomial case

## Related Problems
- [[Graph Traversal Representative Problems]]
- [[BFS - Connected Components]] (similar traversal, different check)

## Related Concepts
- [[Graph Traversal]] — BFS/DFS foundation
- [[BFS]] — level-based coloring approach
- [[DFS]] — recursive coloring approach
- [[Graph Coloring]] — broader theoretical context


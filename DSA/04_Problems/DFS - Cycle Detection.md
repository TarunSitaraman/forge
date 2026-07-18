---
type: problem
status: stable
tags: [dsa/problem, dsa/dfs, dsa/graph]
canonical: true
related: [[[DFS]], [[Graph Traversal]], [[Python - DFS Recursive]]]
difficulty: medium
---
# DFS - Cycle Detection

## Problem Statement
Given a directed graph, determine whether the graph contains a cycle.

A cycle exists if there's a path that starts and ends at the same vertex, following directed edges.

**Constraints:**
- 0 ≤ vertices ≤ 1000
- Edges are directed
- No self-loops or parallel edges (for simplicity)

## Classification
- **Patterns:** [[DFS]], [[Graph Traversal]]
- **Template:** [[Python - DFS Recursive]]
- **Category:** Cycle detection in directed graphs

## Pattern Selection
This is canonical for DFS because:
1. Need to detect back edges (which indicate cycles)
2. DFS discovery/finish times reveal cycle presence
3. Color-based approach (white/gray/black) elegantly tracks state
4. Cannot use simple visited set like undirected; need recursion stack awareness

## Why DFS Works for Cycle Detection
**Invariant:** A back edge (edge to ancestor in recursion tree) indicates a cycle.

**Color States:**
- **White:** Unvisited
- **Gray:** Currently processing (in recursion stack)
- **Black:** Completely finished

**Strategy:**
- Start DFS from each unvisited node
- Color node gray when entering
- If encounter gray node, cycle found (back edge)
- Color node black when exiting

## Python Solution
```python
def hasCycle(vertices: int, edges: list[tuple]) -> bool:
    """
    Detect cycle in directed graph using DFS.
    Time: O(V+E) single DFS traversal
    Space: O(V) for color array and recursion stack
    """
    # Build adjacency list
    graph = [[] for _ in range(vertices)]
    for u, v in edges:
        graph[u].append(v)
    
    # Color states: 0=white, 1=gray, 2=black
    color = [0] * vertices
    
    def dfs(node):
        """
        Returns True if cycle found in subtree rooted at node.
        """
        # Mark as currently processing (gray)
        color[node] = 1
        
        # Explore all neighbors
        for neighbor in graph[node]:
            if color[neighbor] == 1:
                # Back edge found (neighbor in recursion stack)
                return True
            elif color[neighbor] == 0:
                # Unvisited: recursively explore
                if dfs(neighbor):
                    return True
        
        # Mark as finished (black)
        color[node] = 2
        return False
    
    # Check each connected component
    for node in range(vertices):
        if color[node] == 0:
            if dfs(node):
                return True
    
    return False
```

## Reasoning
1. **Build Graph:** Convert edge list to adjacency list
2. **Initialize Colors:** All nodes start white (unvisited)
3. **DFS Traversal:** For each unvisited node, start DFS
4. **Color Gray:** Mark node as currently processing
5. **Explore Neighbors:**
   - If neighbor is gray → back edge found → cycle exists
   - If neighbor is white → recursively explore
6. **Color Black:** Mark node as completely finished
7. **Return:** True if any cycle found, false otherwise

## Complexity Analysis
- **Time:** O(V+E)
  - Each vertex visited once: V
  - Each edge examined once: E
  - Total: V+E

- **Space:** O(V)
  - Color array: O(V)
  - Recursion stack: O(V) worst-case depth
  - Adjacency list: O(V+E) stored

## Edge Cases & Validation
```python
# No cycle
assert hasCycle(3, [(0, 1), (1, 2)]) == False

# Single cycle
assert hasCycle(3, [(0, 1), (1, 2), (2, 0)]) == True

# Self-loop (simple cycle)
assert hasCycle(2, [(0, 0), (0, 1)]) == True

# Disconnected components, no cycles
assert hasCycle(4, [(0, 1), (2, 3)]) == False

# Disconnected with cycle in one component
assert hasCycle(4, [(0, 1), (1, 0), (2, 3)]) == True

# Large DAG (no cycle)
edges = [(i, i+1) for i in range(100)]
assert hasCycle(101, edges) == False

# Triangle cycle
assert hasCycle(3, [(0, 1), (1, 2), (2, 0)]) == True
```

## Common Mistakes
1. **[[Boundary Errors]]:** Not handling all connected components
   - Must check every node, not just reachable from node 0

2. **[[Missing Visited Set]]:** Using only one visited array
   - Need three states (white/gray/black), not just visited/unvisited
   - Simple "visited" misses back edges in complex graphs

3. **[[Infinite Loop]]:** Not properly marking nodes
   - Must update color when entering and exiting
   - Otherwise infinite recursion

4. **Not Exploring All Neighbors:**
   - Continue DFS even after finding one cycle possibility
   - Must complete traversal of current node's neighbors

## Interview Walkthrough
**Interviewer:** "Detect if directed graph has a cycle."

**Your Response:**
1. "Cycle means a path that returns to itself—a back edge in DFS tree."
2. "I'll use three colors: white (unvisited), gray (currently visiting), black (done)."
3. "If I encounter a gray node from current DFS, that's a back edge—cycle found."
4. "Time is O(V+E) since each node and edge visited once."

**Why This Resonates:**
- Shows understanding of DFS tree structure
- Explains how back edges indicate cycles
- Demonstrates color-based state tracking
- Correctly handles disconnected components

## Variations
1. **Find Cycle Path:** Store parent pointers; backtrack when cycle found
2. **Count Cycles:** Harder; need to avoid counting same cycle multiple times
3. **Strongly Connected Components:** Kosaraju's algorithm uses cycle detection
4. **Topological Sort Validation:** Successful topo sort means no cycles

## Related Problems
- [[DFS - Number of Islands]] (connected components without cycles)
- [[Graph Traversal - Cycle Detection]] (undirected variant)
- [[Topological Sort - Cycle Detection]] (DAG validation)
- [[DFS Representative Problems]]

## Related Concepts
- [[DFS]] — fundamental graph traversal
- [[Back Edges]] — indicate cycles
- [[Recursion Stack]] — enables cycle detection
- [[Graph Theory]] — cycle properties


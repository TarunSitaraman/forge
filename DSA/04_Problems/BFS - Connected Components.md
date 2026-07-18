---
type: problem
status: stable
tags: [dsa/problem, dsa/bfs, dsa/graph]
canonical: true
related: [[[BFS]], [[Graph Traversal]], [[Union Find]]]
difficulty: medium
---
# BFS - Connected Components (Number of Provinces)

## Problem Statement
Given an n×n matrix `isConnected` where `isConnected[i][j] = 1` if city i and city j are directly connected, return the total number of provinces (connected groups of cities).

**Constraints:**
- 1 ≤ n ≤ 200
- isConnected[i][i] = 1 (city always connected to itself)
- isConnected is symmetric (undirected graph)

## Classification
- **Patterns:** [[BFS]], [[Graph Traversal]]
- **Related:** [[Union Find]] (alternative approach)
- **Category:** Connected components counting via BFS

## Pattern Selection
This is canonical for BFS connected components because:
1. Matrix represents adjacency (dense graph representation)
2. Each unvisited city starts a new BFS, representing one province
3. BFS explores all cities reachable from starting city (same province)
4. Count of BFS initiations = count of provinces

## Why BFS Works
**Invariant:** All cities visited in one BFS traversal belong to the same connected component (province).

**Strategy:**
- For each unvisited city, start BFS
- BFS marks all directly/indirectly connected cities as visited
- Increment province count once per BFS initiation
- Continue until all cities visited

## Python Solution
```python
from collections import deque

def findCircleNum(isConnected: list[list[int]]) -> int:
    """
    Count number of provinces (connected components) using BFS.
    Time: O(n^2) examining adjacency matrix
    Space: O(n) for visited set and queue
    """
    n = len(isConnected)
    visited = [False] * n
    provinces = 0
    
    def bfs(start):
        """Mark all cities in same province as visited."""
        queue = deque([start])
        visited[start] = True
        
        while queue:
            city = queue.popleft()
            
            for neighbor in range(n):
                if isConnected[city][neighbor] == 1 and not visited[neighbor]:
                    visited[neighbor] = True
                    queue.append(neighbor)
    
    for city in range(n):
        if not visited[city]:
            bfs(city)
            provinces += 1
    
    return provinces
```

## Reasoning
1. **Initialize:** Visited array tracks which cities have been assigned to a province
2. **Scan Cities:** For each unvisited city, this represents start of new province
3. **BFS Exploration:** Mark all directly/transitively connected cities as visited
4. **Count Provinces:** Increment counter each time we start a new BFS
5. **Termination:** All cities visited means all provinces found

## Complexity Analysis
- **Time:** O(n²)
  - For each city, examine n potential neighbors: O(n) per city
  - Total across all cities: O(n²)

- **Space:** O(n)
  - Visited array: O(n)
  - Queue: O(n) worst case (all cities in one province)

## Edge Cases & Validation
```python
# All connected (one province)
isConnected = [[1,1,1],[1,1,1],[1,1,1]]
assert findCircleNum(isConnected) == 1

# All separate (n provinces)
isConnected = [[1,0,0],[0,1,0],[0,0,1]]
assert findCircleNum(isConnected) == 3

# Mixed connectivity
isConnected = [[1,1,0],[1,1,0],[0,0,1]]
assert findCircleNum(isConnected) == 2

# Single city
assert findCircleNum([[1]]) == 1

# Transitive connectivity (A-B, B-C means A-B-C connected)
isConnected = [[1,1,0],[1,1,1],[0,1,1]]
assert findCircleNum(isConnected) == 1  # All in one province via B
```

## Common Mistakes
1. **[[Missing Visited Set]]:** Not tracking visited cities
   - Without this, same province gets counted multiple times

2. **Confusing Direct vs. Transitive Connection:**
   - isConnected[i][j]=1 means DIRECT connection
   - But BFS correctly handles TRANSITIVE connections (A-B-C chain)

3. **Not Handling Self-Connection:**
   - isConnected[i][i] = 1 is given; doesn't affect algorithm but shouldn't confuse logic

4. **[[Off-by-One]]:** Matrix indexing errors
   - Careful with 0-indexed vs. 1-indexed city numbering

## Interview Walkthrough
**Interviewer:** "Count number of provinces (connected city groups)."

**Your Response:**
1. "Each unvisited city represents a new province—I'll start BFS from there."
2. "BFS explores all cities reachable through direct or transitive connections."
3. "I mark all visited cities during BFS, ensuring they're not recounted."
4. "Total BFS initiations equals total provinces."

**Why This Resonates:**
- Shows understanding that BFS naturally captures transitive connectivity
- Explains counting logic clearly (count BFS starts, not cities)

## Variations
1. **Union-Find Approach:** Alternative using disjoint set instead of BFS
2. **Adjacency List Instead of Matrix:** More efficient for sparse graphs O(V+E) vs O(V²)
3. **Weighted Provinces:** Find province with maximum size/weight
4. **Dynamic Connectivity:** Handle addition of new connections over time (Union-Find better here)

## Related Problems
- [[Union Find - Number of Connected Components]] (alternative approach, same problem)
- [[DFS - Number of Islands]] (similar pattern, grid instead of matrix)
- [[BFS Representative Problems]]

## Related Concepts
- [[BFS]] — traversal mechanism
- [[Union Find]] — alternative data structure approach
- [[Graph Traversal]] — broader context


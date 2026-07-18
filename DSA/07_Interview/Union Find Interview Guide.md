---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/union-find]
canonical: true
related: [[[Union Find]], [[Coding Interviews]], [[Communicating Thought Process]]]
---
# Union Find Interview Guide

## Pattern Recognition (30 seconds)

**Green Flags:**
- "Connected", "same group", "same component" queries
- Incremental edge addition with connectivity checks
- Minimum spanning tree (Kruskal's)
- Cycle detection in undirected graph

**Red Flags (probably NOT Union-Find):**
- Directed graph cycle detection → DFS with coloring
- Need actual path, not just connectivity → BFS/DFS

## Communication Template

```
Interviewer: "Count connected components after adding edges."

You: "Union-Find is ideal here—O(α(n)) per operation with path 
compression and union by rank. I'll union endpoints of each edge, 
then count distinct roots at the end."
```

## Core Mechanics

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n
    
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # Path compression
        return self.parent[x]
    
    def union(self, x, y):
        root_x, root_y = self.find(x), self.find(y)
        if root_x == root_y:
            return False  # Already connected (cycle if edge)
        
        # Union by rank
        if self.rank[root_x] < self.rank[root_y]:
            root_x, root_y = root_y, root_x
        self.parent[root_y] = root_x
        if self.rank[root_x] == self.rank[root_y]:
            self.rank[root_x] += 1
        return True
```

## Common Mistakes

1. **Missing path compression:** O(n) find instead of O(α(n))
2. **Missing union by rank:** Trees become chains, degrading performance
3. **Forgetting cycle = union returns False:** Key insight for cycle detection

## Practice Problems
- Number of Connected Components
- Redundant Connection (cycle detection)
- Kruskal's MST

## The Checklist
- [ ] Implemented path compression in find()
- [ ] Implemented union by rank/size
- [ ] Used union's return value for cycle detection if needed
- [ ] Confirmed O(α(n)) amortized complexity

## Related Content
- [[Union Find]] — Pattern deep dive
- [[Union Find Representative Problems]]


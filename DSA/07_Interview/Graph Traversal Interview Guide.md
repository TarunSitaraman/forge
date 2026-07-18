---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/graph-traversal]
canonical: true
related: [[[Graph Traversal]], [[Coding Interviews]], [[Communicating Thought Process]]]
---
# Graph Traversal Interview Guide

## Pattern Recognition (30 seconds)

**Green Flags:**
- "Connected", "reachable", "component" language
- Cycle detection needed
- Topological ordering / dependency resolution

**Key Decision: DFS or BFS?**
| Need | Approach |
|------|----------|
| Shortest path (unweighted) | BFS |
| Any path / all paths | DFS |
| Cycle detection (directed) | DFS with color states |
| Cycle detection (undirected) | Union-Find or DFS with parent tracking |
| Topological sort | DFS post-order OR Kahn's (BFS) |

## Communication Template

```
Interviewer: "Determine if graph is bipartite."

You: "I'll use BFS/DFS to 2-color the graph. Starting from any node,
assign color A, then all neighbors get color B, and so on.
If I ever find an edge between two same-colored nodes, it's not bipartite."
```

## Core Mechanics

```python
def dfs_component(node, graph, visited):
    visited.add(node)
    for neighbor in graph[node]:
        if neighbor not in visited:
            dfs_component(neighbor, graph, visited)

def count_components(graph, n):
    visited = set()
    count = 0
    for node in range(n):
        if node not in visited:
            dfs_component(node, graph, visited)
            count += 1
    return count
```

## Common Mistakes

1. **Wrong cycle detection for graph type:** Directed needs 3-color, undirected needs parent tracking
2. **Missing disconnected components:** Only traversing from node 0
3. **Confusing adjacency representation:** List vs matrix affects complexity

## Practice Problems
- Number of Connected Components
- Course Schedule (cycle detection + topo sort)
- Is Graph Bipartite

## The Checklist
- [ ] Chose DFS or BFS based on shortest-path requirement
- [ ] Handled all disconnected components
- [ ] Used correct cycle detection for directed/undirected
- [ ] Confirmed O(V+E) complexity

## Related Content
- [[Graph Traversal]] — Pattern deep dive
- [[Graph Traversal Representative Problems]]


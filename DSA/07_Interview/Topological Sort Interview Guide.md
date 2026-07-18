---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/topological-sort]
canonical: true
related: [[[Topological Sort]], [[Coding Interviews]]]
---
# Topological Sort Interview Guide

## Pattern Recognition
**Green Flags:** `course prerequisites`, `build order`, `dependency resolution`, DAG ordering

## Communication Template
`I'll use Kahn's algorithm: track in-degree for each node, process nodes with in-degree 0, decrementing neighbors' in-degree as I go. If all nodes processed, valid topological order exists; otherwise cycle detected.`

## Core Mechanics
```python
from collections import deque

def topological_sort(n, edges):
    graph = [[] for _ in range(n)]
    in_degree = [0] * n
    for u, v in edges:
        graph[u].append(v)
        in_degree[v] += 1
    
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    result = []
    
    while queue:
        node = queue.popleft()
        result.append(node)
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    return result if len(result) == n else []  # Empty if cycle exists
```

## Common Mistakes
1. Not detecting cycles (result length != n means cycle)
2. Confusing in-degree with out-degree
3. Not initializing all in-degree 0 nodes correctly

## Practice Problems
- Course Schedule, Alien Dictionary, Build Order

## Checklist
- [ ] Verified cycle detection via result length check
- [ ] Correct in-degree tracking
- [ ] O(V+E) complexity confirmed

## Related
- [[Topological Sort]]
- [[Topological Sort Representative Problems]]

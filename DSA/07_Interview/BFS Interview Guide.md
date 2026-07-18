---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/bfs]
canonical: true
related: [[[BFS]], [[Coding Interviews]], [[Communicating Thought Process]]]
---
# BFS Interview Guide

This guide prepares you to communicate and defend BFS solutions in technical interviews.

## Pattern Recognition (30 seconds)

**Green Flags for BFS:**
- "Shortest path" in unweighted graph
- "Minimum steps/moves" to reach target
- "Level by level" processing
- Multi-source simultaneous spread (rotting oranges, infection spread)

**Yellow Flags (may be BFS, may not):**
- "All paths" — likely DFS instead (BFS doesn't naturally enumerate all paths)
- Deep exploration needed — DFS may use less memory

**Red Flags (probably NOT BFS):**
- Weighted graph shortest path → Dijkstra
- "All combinations" → DFS/Backtracking

## Communication Template

```
Interviewer: "Find shortest path in unweighted graph."

You: "Since all edges have equal weight, BFS guarantees shortest 
path because it explores nodes in order of increasing distance.

Strategy:
1. Start with source in queue, distance 0
2. Process nodes level by level (FIFO ensures this)
3. First time reaching a node = shortest distance (never revisit)
4. Track distance as: distance[neighbor] = distance[current] + 1"
```

## The Core Mechanics

```python
from collections import deque

def bfs(start, graph):
    visited = {start}
    queue = deque([start])
    distance = {start: 0}
    
    while queue:
        node = queue.popleft()
        
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)  # Mark when ENQUEUING
                distance[neighbor] = distance[node] + 1
                queue.append(neighbor)
    
    return distance
```

## Variations & Pressure Test

**Interviewer:** "Multiple starting points simultaneously?"

Your Response:
- "Initialize queue with ALL sources at once, not sequentially."
- "This ensures correct simultaneous timing (e.g., rotting oranges spreading together)."

**Interviewer:** "What if some edges have weight 0 and others weight 1?"

Your Response:
- "Use 0-1 BFS with a deque: push 0-weight neighbors to front, 1-weight to back."
- "This maintains BFS's layer-order property even with two edge weights."

**Interviewer:** "Memory concerns with BFS?"

Your Response:
- "BFS queue can hold O(V) nodes at widest level—more memory than DFS's O(depth)."
- "For very wide graphs, this could be a concern; DFS might be preferred if shortest path isn't required."

## Common Mistakes

### Mistake 1: Marking Visited at Wrong Time
```python
# ❌ WRONG - marks when processing (allows duplicates in queue)
while queue:
    node = queue.popleft()
    visited.add(node)  # Too late!
    for neighbor in graph[node]:
        if neighbor not in visited:
            queue.append(neighbor)

# ✓ CORRECT - marks when enqueueing
while queue:
    node = queue.popleft()
    for neighbor in graph[node]:
        if neighbor not in visited:
            visited.add(neighbor)  # Mark immediately
            queue.append(neighbor)
```

### Mistake 2: Using List Instead of Deque
```python
# ❌ WRONG - list.pop(0) is O(n), makes BFS O(n²)
queue = []
queue.pop(0)

# ✓ CORRECT - deque.popleft() is O(1)
from collections import deque
queue = deque()
queue.popleft()
```

### Mistake 3: Missing Level Boundary Tracking (Level-Order Problems)
```python
# ❌ WRONG - doesn't distinguish levels
while queue:
    node = queue.popleft()
    process(node)

# ✓ CORRECT - captures level size BEFORE processing
while queue:
    level_size = len(queue)  # Critical: before the loop
    for _ in range(level_size):
        node = queue.popleft()
        process(node)
```

## Practice Problems By Category

### Category 1: Shortest Path
- Shortest Path in Unweighted Graph
- Word Ladder
- Minimum Knight Moves

### Category 2: Level Processing
- Level Order Traversal
- Zigzag Level Order
- Right Side View

### Category 3: Multi-Source
- Rotting Oranges
- Walls and Gates
- 01 Matrix

## The Checklist

- [ ] Explained why BFS guarantees shortest path (not DFS)
- [ ] Used deque, not list, for O(1) operations
- [ ] Marked visited at correct time (when enqueueing)
- [ ] Tracked level boundaries if problem requires it
- [ ] Handled multi-source initialization correctly if needed

## Related Interview Content
- [[Coding Interviews]]
- [[Communicating Thought Process]]
- [[BFS]] — Pattern deep dive
- [[BFS Representative Problems]] — Practice problems


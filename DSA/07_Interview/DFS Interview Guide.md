---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/dfs]
canonical: true
related: [[[DFS]], [[Coding Interviews]], [[Communicating Thought Process]]]
---
# DFS Interview Guide

This guide prepares you to communicate and defend DFS solutions in technical interviews.

## Pattern Recognition (30 seconds)

**Green Flags for DFS:**
- "Explore all paths" or "all possible combinations"
- Tree/graph traversal where path matters
- Backtracking required (permutations, combinations, N-Queens)
- Connected components or reachability
- Deep exploration before considering alternatives

**Yellow Flags (may be DFS, may not):**
- "Shortest path" — likely BFS instead (DFS doesn't guarantee shortest)
- "Level by level" — likely BFS instead

**Red Flags (probably NOT DFS):**
- "Minimum steps" in unweighted graph → BFS
- Simple linear scan suffices → no traversal needed

## Communication Template

```
Interviewer: "Find all paths from root to leaves."

You: "This requires exploring every path completely before 
backtracking, so DFS is the right choice.

Strategy:
1. Recursively explore left, then right subtree
2. Track current path state
3. At leaf, record the path
4. Backtrack: remove current node before returning to parent

This naturally handles the 'explore-then-backtrack' requirement."
```

## The Core Mechanics

```python
def dfs(node, state):
    # Base case: null or termination condition
    if not node:
        return
    
    # Process current node (pre-order position)
    state.append(node.val)
    
    # Recurse into children
    dfs(node.left, state)
    dfs(node.right, state)
    
    # Backtrack (if needed): undo state change
    state.pop()
```

## Variations & Pressure Test

**Interviewer:** "Can you do this iteratively instead of recursively?"

Your Response:
- "Yes, using an explicit stack instead of the call stack."
- "Push node, when popped, push children (order matters for pre/in/post-order)."
- "Trade-off: avoids stack overflow risk, but more verbose."

**Interviewer:** "What if the graph has cycles?"

Your Response:
- "Need a visited set to prevent infinite loops."
- "Mark visited when entering a node (not necessarily when finishing)."
- "For directed graphs with cycle detection, need 3-state coloring."

**Interviewer:** "Stack overflow risk with deep recursion?"

Your Response:
- "Convert to iterative with explicit stack, or increase recursion limit."
- "Python's default recursion limit is ~1000; deep trees may hit this."

## Common Mistakes

### Mistake 1: Missing Visited Set (Graphs)
```python
# ❌ WRONG - infinite loop on cycles
def dfs(node):
    for neighbor in graph[node]:
        dfs(neighbor)

# ✓ CORRECT
def dfs(node, visited):
    visited.add(node)
    for neighbor in graph[node]:
        if neighbor not in visited:
            dfs(neighbor, visited)
```

### Mistake 2: Forgetting to Backtrack
```python
# ❌ WRONG - path state corrupted for future branches
def dfs(node, path):
    path.append(node.val)
    dfs(node.left, path)
    dfs(node.right, path)
    # Missing: path.pop()

# ✓ CORRECT
def dfs(node, path):
    path.append(node.val)
    dfs(node.left, path)
    dfs(node.right, path)
    path.pop()  # Restore state for sibling exploration
```

### Mistake 3: Wrong Order for Backtracking (Mutable Defaults)
```python
# ❌ WRONG - appends reference, later mutated
result.append(current_path)

# ✓ CORRECT - appends snapshot
result.append(current_path[:])
```

## Practice Problems By Category

### Category 1: Graph Reachability
- Number of Islands
- Connected Components
- Cycle Detection

### Category 2: Tree Path Problems
- Path Sum
- All Root-to-Leaf Paths
- Diameter of Tree

### Category 3: Backtracking
- Permutations
- Combinations
- Word Search
- N-Queens

## The Checklist

- [ ] Explained why DFS (not BFS) fits this problem
- [ ] Addressed visited/cycle handling if graph-based
- [ ] Showed backtracking logic if state must be undone
- [ ] Confirmed O(V+E) or O(n) complexity with justification
- [ ] Tested edge cases (empty tree/graph, single node, cycles)

## Related Interview Content
- [[Coding Interviews]]
- [[Communicating Thought Process]]
- [[DFS]] — Pattern deep dive
- [[DFS Representative Problems]] — Practice problems


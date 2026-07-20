---
type: problem
status: stable
tags: [dsa/problem, dsa/graph-traversal, dsa/dfs]
canonical: true
related: [[[Graph Traversal]], [[DFS]], [[Topological Sort]]]
difficulty: medium
---
# Graph Traversal - Course Schedule (Cycle Detection)

## Problem Statement
Given `numCourses` and a list of prerequisite pairs `[a, b]` (must take
`b` before `a`), determine whether it's possible to finish all courses.

**Constraints:**
- Prerequisites form a directed graph
- Possible to finish iff the graph has no cycle (it's a DAG)

## Classification
- **Patterns:** [[Graph Traversal]], [[DFS]]
- **Category:** Directed-graph cycle detection via 3-color DFS

## Pattern Selection
This is the canonical directed-cycle-detection problem: unlike undirected
graphs (where "visited" alone suffices), a directed graph needs to
distinguish "currently on the recursion stack" from "fully processed
and safe" — otherwise cross edges get misread as cycles.

## Why 3-Color DFS Works
**States:** white (unvisited), gray (on current DFS path), black (fully
explored, safe).

**Key insight:** a cycle exists iff DFS ever reaches a **gray** node — that
means we've looped back to something still on the current path. Reaching
a **black** node is fine — it's a previously-verified-safe cross edge, not
a cycle.

## Python Solution
```python
def canFinish(numCourses: int, prerequisites: list[list[int]]) -> bool:
    """
    Detect cycle in prerequisite graph using 3-color DFS.
    Time: O(V+E)
    Space: O(V+E) for graph + O(V) for color array/recursion stack
    """
    graph = [[] for _ in range(numCourses)]
    for course, prereq in prerequisites:
        graph[prereq].append(course)

    WHITE, GRAY, BLACK = 0, 1, 2
    color = [WHITE] * numCourses

    def dfs(node):
        color[node] = GRAY
        for neighbor in graph[node]:
            if color[neighbor] == GRAY:
                return False  # back edge -> cycle
            if color[neighbor] == WHITE and not dfs(neighbor):
                return False
        color[node] = BLACK
        return True

    return all(color[c] != WHITE or dfs(c) for c in range(numCourses))
```

## Reasoning
1. **Build graph:** prereq → course edges.
2. **DFS with 3 colors:** mark gray on entry, black on exit.
3. **Back edge check:** hitting a gray neighbor means a cycle.
4. **Check every component:** courses with no prerequisites (or
   unreachable from earlier starts) still need their own DFS.

## Complexity Analysis
- **Time:** O(V+E) — each node/edge visited once
- **Space:** O(V+E) — adjacency list + O(V) color array/recursion

## Edge Cases & Validation
```python
assert canFinish(2, [[1,0]]) == True          # 0 -> 1, no cycle
assert canFinish(2, [[1,0],[0,1]]) == False   # 0 -> 1 -> 0, cycle
assert canFinish(1, []) == True               # no prerequisites
assert canFinish(3, [[1,0],[2,1]]) == True    # chain, no cycle
```

## Common Mistakes
1. **Using a single visited set (like undirected cycle detection):**
   misses the distinction between "still on this path" and "already
   safely finished" — causes false positives on valid DAGs with
   diamond-shaped dependencies (shared prerequisites reached twice).
2. **Forgetting to mark black on exit:** node stays gray forever, so any
   later legitimate path through it looks like a cycle.
3. **Not iterating all starting nodes:** disconnected components get
   skipped, missing real cycles.

## Interview Walkthrough
**Interviewer:** "Can all courses be completed given these prerequisites?"

**Your Response:**
1. "This is cycle detection in a directed graph — a valid schedule exists
   iff the prerequisite graph is a DAG."
2. "I use 3-color DFS: gray means 'on my current path,' black means
   'fully verified safe.'"
3. "Hitting a gray node means I've looped back onto my own path — that's
   a cycle. Hitting a black node is just a normal shared dependency, not
   a cycle."
4. "I run this from every unvisited node to cover disconnected parts of
   the graph."

## Variations
1. **Course Schedule II:** return a valid ordering, not just
   feasibility — this is exactly [[Topological Sort]]
2. **Find the cycle itself:** track parent pointers to reconstruct the
   actual cycle path when one is found

## Related Problems
- [[Topological Sort - Course Schedule]] (same setup, ordering instead
  of just feasibility)
- [[DFS - Cycle Detection]] (same technique, framed as pure DFS)
- [[Graph Traversal Representative Problems]]

## Related Concepts
- [[Graph Traversal]] — DFS-based directed cycle detection
- [[Topological Sort]] — natural next step once feasibility is confirmed


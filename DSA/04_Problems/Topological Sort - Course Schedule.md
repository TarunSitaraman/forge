---
type: problem
status: stable
tags: [dsa/problem, dsa/topological-sort-course-schedule]
canonical: true
related: [[[Topological Sort]], [[Graph Traversal]], [[Python - Topological Sort]]]
---
# Topological Sort - Course Schedule

## Problem Statement
Decide whether all courses can be completed given prerequisites.

## Classification
- Patterns: [[Topological Sort]], [[Graph Traversal]]
- Template: [[Python - Topological Sort]]

## Pattern Selection
If Kahn processes fewer than n nodes, a cycle prevents a valid order. This page applies canonical theory rather than duplicating it.

## Python Solution
``python
from collections import deque

def can_finish(num_courses, prerequisites):
    graph = [[] for _ in range(num_courses)]
    indegree = [0] * num_courses
    for course, prereq in prerequisites:
        graph[prereq].append(course)
        indegree[course] += 1
    q = deque(i for i, deg in enumerate(indegree) if deg == 0)
    seen = 0
    while q:
        node = q.popleft()
        seen += 1
        for nxt in graph[node]:
            indegree[nxt] -= 1
            if indegree[nxt] == 0:
                q.append(nxt)
    return seen == num_courses
``

## Reasoning
The solution preserves the invariant owned by the linked pattern page, advances monotonically through the input or state space, and records only the state required to produce the answer.

## Complexity
O(V+E) time and O(V+E) space.

## Lessons
- State the invariant before coding.
- Keep boundary handling explicit.
- Link reusable reasoning back to canonical pages.

## Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Missing Visited Set]]

## Related Problems
- [[Problem Index]]
- [[Graph Traversal - Course Schedule]] — same problem solved via 3-color DFS instead of Kahn's BFS; useful to know both when an interviewer asks for an alternative approach


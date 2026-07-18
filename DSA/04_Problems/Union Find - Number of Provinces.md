---
type: problem
status: stable
tags: [dsa/problem, dsa/union-find-number-of-provinces]
canonical: true
related: [[[Union Find]], [[Graph Traversal]], [[Python - Union Find]]]
---
# Union Find - Number of Provinces

## Problem Statement
Count connected components in an adjacency matrix.

## Classification
- Patterns: [[Union Find]], [[Graph Traversal]]
- Template: [[Python - Union Find]]

## Pattern Selection
Union every connected pair, then count unique representatives. This page applies canonical theory rather than duplicating it.

## Python Solution
``python
def find_circle_num(is_connected):
    n = len(is_connected)
    parent = list(range(n))
    def find(x):
        while x != parent[x]:
            parent[x] = parent[parent[x]]
            x = parent[x]
        return x
    def union(a, b):
        parent[find(a)] = find(b)
    for i in range(n):
        for j in range(i + 1, n):
            if is_connected[i][j]:
                union(i, j)
    return len({find(i) for i in range(n)})
``

## Reasoning
The solution preserves the invariant owned by the linked pattern page, advances monotonically through the input or state space, and records only the state required to produce the answer.

## Complexity
O(n^2 alpha(n)) time and O(n) space.

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


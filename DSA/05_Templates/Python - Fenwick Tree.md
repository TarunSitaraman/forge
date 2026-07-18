---
type: python-template
status: stable
tags: [dsa/template, python]
canonical: true
---
# Python - Fenwick Tree

## Purpose
Reusable interview-ready Python template for [[Fenwick Tree]].

## Complexity
- Time: Depends on operation and input size; document per problem.
- Space: Depends on auxiliary structures; document per problem.

## Code
``python
class FenwickTree:
    def __init__(self, n):
        self.tree = [0] * (n + 1)

    def add(self, index, delta):
        i = index + 1
        while i < len(self.tree):
            self.tree[i] += delta
            i += i & -i

    def prefix_sum(self, index):
        total = 0
        i = index + 1
        while i > 0:
            total += self.tree[i]
            i -= i & -i
        return total
``

## Edge Cases
- Empty input
- Single-element input
- Duplicate values or repeated states
- Disconnected components when graph-shaped

## Common Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Missing Visited Set]]


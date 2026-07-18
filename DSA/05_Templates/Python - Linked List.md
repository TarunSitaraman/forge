---
type: python-template
status: stable
tags: [dsa/template, python]
canonical: true
---
# Python - Linked List

## Purpose
Reusable interview-ready Python template for [[Linked List]].

## Complexity
- Time: Depends on operation and input size; document per problem.
- Space: Depends on auxiliary structures; document per problem.

## Code
``python
def reverse_list(head):
    prev = None
    cur = head
    while cur:
        nxt = cur.next
        cur.next = prev
        prev = cur
        cur = nxt
    return prev
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


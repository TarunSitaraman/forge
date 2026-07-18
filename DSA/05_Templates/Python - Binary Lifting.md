---
type: python-template
status: stable
tags: [dsa/template, python, dsa/binary-lifting]
canonical: true
related: [[Binary Lifting]]
---
# Python - Binary Lifting

## Purpose
Precompute powers-of-two jumps over parent pointers.

## Explanation
This template captures the reusable implementation shape for [[Binary Lifting]]. Keep problem-specific naming and validation in the problem page, but preserve the invariant and boundary discipline shown here.

## Complexity
O(n log n) preprocessing, O(log n) query.

## Code
``python
def build_lift(parent):
    n = len(parent)
    log = max(1, n.bit_length())
    up = [parent[:]]
    for level in range(1, log):
        prev = up[-1]
        up.append([prev[prev[v]] if prev[v] != -1 else -1 for v in range(n)])
    return up

def kth_ancestor(up, node, k):
    bit = 0
    while k and node != -1:
        if k & 1:
            node = up[bit][node]
        k >>= 1
        bit += 1
    return node
``

## Edge Cases
- Empty input
- Single-element input
- Duplicate values or repeated states
- Inclusive versus exclusive boundary semantics

## Modifications
Add depths for LCA; adapt to functional graphs with cycle handling.

## Common Interview Variations
- Return the count instead of the object
- Return all valid objects instead of the best one
- Add online updates or streaming input
- Tighten memory from O(n) to O(1) when dependencies allow it

## Common Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Infinite Loop]]


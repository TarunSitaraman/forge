---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/binary-lifting]
canonical: true
related: [[[Binary Lifting]], [[Coding Interviews]]]
---
# Binary Lifting Interview Guide

## Pattern Recognition
**Green Flags:** Tree ancestor queries; LCA with multiple queries; `kth ancestor` type problems needing O(log n) per query

## Communication Template
`I'll precompute up[node][j] = 2^j-th ancestor of node using dynamic programming: up[node][j] = up[up[node][j-1]][j-1]. This enables O(log n) ancestor queries via binary representation of k.`

## Core Mechanics
```python
def build_binary_lifting(n, parent, LOG=20):
    up = [[0] * LOG for _ in range(n)]
    for i in range(n):
        up[i][0] = parent[i]
    
    for j in range(1, LOG):
        for i in range(n):
            up[i][j] = up[up[i][j-1]][j-1]
    return up

def kth_ancestor(up, node, k, LOG=20):
    for j in range(LOG):
        if k & (1 << j):
            node = up[node][j]
    return node
```

## Common Mistakes
1. Not precomputing table before queries (defeats O(log n) per query benefit)
2. Wrong LOG bound (must cover log2(max_depth))
3. Off-by-one in ancestor indexing (0-indexed jumps)

## Practice Problems
- Kth Ancestor of Tree Node, LCA with binary lifting

## Checklist
- [ ] Precomputed table before answering queries
- [ ] LOG value sufficient for tree depth
- [ ] O(n log n) preprocessing, O(log n) per query confirmed

## Related
- [[Binary Lifting]]

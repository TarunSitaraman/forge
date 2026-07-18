---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/tree-traversal]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Tree Traversal Cheat Sheet

## Signals
Tree structure; need sorted access (BST); path-based problems; level-based grouping.

## Core Moves
- Inorder: Left-Root-Right (sorted BST access)
- Preorder: Root-Left-Right (copy/serialize)
- Postorder: Left-Right-Root (delete/aggregate)
- Level-order: BFS with queue

## Complexity
O(n) time, O(h) space (h=height) for DFS; O(n) space worst-case BFS.

## Template Links
- [[Python - DFS Recursive]]

## Watch For
- [[Incorrect Base Case]]
- Path state not restored (backtracking)

## Canonical Depth
See [[Tree Traversal]] for full explanation.

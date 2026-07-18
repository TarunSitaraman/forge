---
type: problem
status: stable
tags: [dsa/problem, dsa/tree-traversal]
canonical: true
related: [[[Tree Traversal]], [[DFS]], [[Python - DFS Recursive]]]
difficulty: medium
---
# Tree Traversal - Diameter of Tree

## Problem Statement
Given a binary tree, return the length of the diameter — the longest path between any two nodes (may or may not pass through root). Path length measured in number of edges.

**Constraints:**
- Tree may be empty (diameter = 0)
- Path doesn't need to pass through root
- Path length = number of edges, not nodes

## Classification
- **Patterns:** [[Tree Traversal]], [[DFS]]
- **Template:** [[Python - DFS Recursive]]
- **Category:** Tree metric computation with global state tracking

## Pattern Selection
This is canonical for the "compute height while tracking global maximum" pattern:
1. At each node, diameter through that node = left_height + right_height
2. Must track global maximum separately from the height return value
3. Height (returned to parent) differs from diameter (tracked globally)

## Why This Approach Works
**Invariant:** At each node, we compute height (needed by parent) while simultaneously checking if diameter through this node beats global maximum.

**Key Insight - Dual Purpose Recursion:**
- Return value: height of subtree (what parent needs for its own calculation)
- Side effect: update global max_diameter if current node's diameter is larger

## Python Solution
```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def diameterOfBinaryTree(root: TreeNode) -> int:
    """
    Find diameter (longest path) in binary tree.
    Time: O(n) visit each node once
    Space: O(h) recursion depth
    """
    max_diameter = [0]  # Use list for closure mutability
    
    def height(node):
        """
        Returns height of subtree rooted at node.
        Side effect: updates max_diameter if applicable.
        """
        if not node:
            return 0
        
        left_height = height(node.left)
        right_height = height(node.right)
        
        # Diameter through this node = sum of both subtree heights
        current_diameter = left_height + right_height
        max_diameter[0] = max(max_diameter[0], current_diameter)
        
        # Return height (not diameter) to parent
        return 1 + max(left_height, right_height)
    
    height(root)
    return max_diameter[0]
```

## Reasoning
1. **Recursive Height Calculation:** Standard tree height computation
2. **Side Effect Tracking:** At each node, calculate diameter through that node (left_height + right_height)
3. **Global Maximum:** Update running maximum diameter across all nodes
4. **Return Value Distinction:** Function returns HEIGHT (for parent's calculation), not diameter
5. **Final Answer:** After full traversal, max_diameter holds the answer

## Complexity Analysis
- **Time:** O(n) — each node visited once, O(1) work per node
- **Space:** O(h) — recursion stack depth (h = height, worst case O(n) for skewed tree)

## Edge Cases & Validation
```python
# Empty tree
assert diameterOfBinaryTree(None) == 0

# Single node (no edges, diameter = 0)
assert diameterOfBinaryTree(TreeNode(1)) == 0

# Simple path: diameter passes through root
#     1
#    / \
#   2   3
root = TreeNode(1, TreeNode(2), TreeNode(3))
assert diameterOfBinaryTree(root) == 2  # Path: 2-1-3

# Diameter NOT through root (classic tricky case)
#         1
#        /
#       2
#      / \
#     3   4
#    /
#   5
root = TreeNode(1, TreeNode(2, TreeNode(3, TreeNode(5)), TreeNode(4)))
assert diameterOfBinaryTree(root) == 3  # Path: 5-3-2-4

# Skewed tree (linked-list-like)
root = TreeNode(1, TreeNode(2, TreeNode(3, TreeNode(4))))
assert diameterOfBinaryTree(root) == 3  # Path: 4-3-2-1
```

## Common Mistakes
1. **Confusing Diameter with Height:** These are different values requiring separate tracking
2. **[[Incorrect Base Case]]:** Height of null node should be 0, not -1 (though -1 works with adjusted formula)
3. **Not Considering Non-Root Diameter:** Classic mistake—assuming diameter always passes through root
4. **Mutable Default Pitfall Avoided:** Using list `[0]` for closure mutability instead of nonlocal (both work, list often clearer)

## Interview Walkthrough
**Interviewer:** "Find the diameter of a binary tree."

**Your Response:**
1. "Diameter might not pass through the root—I need to check every node as potential 'peak'."
2. "For each node, diameter through it equals left subtree height plus right subtree height."
3. "I'll compute height recursively while tracking the maximum diameter seen so far."
4. "The function returns height (for parent's use), but has a side effect of updating global max."

**Why This Resonates:**
- Shows awareness of common pitfall (diameter not always through root)
- Explains the elegant dual-purpose recursion (return height, track diameter separately)
- Demonstrates clean separation of concerns in recursive design

## Variations
1. **Diameter of N-ary Tree:** Generalize to nodes with multiple children
2. **Path Sum Along Diameter:** Track actual values along longest path, not just length
3. **Weighted Edges:** Diameter considering edge weights instead of just counting edges

## Related Problems
- [[Tree Traversal - Lowest Common Ancestor]] (different tree metric computation)
- [[DFS - Path Sum]] (different global state tracking during DFS)
- [[Tree Traversal Representative Problems]]

## Related Concepts
- [[Tree Traversal]] — DFS-based height computation
- [[DFS]] — recursive exploration with side effects
- [[Recursion]] — dual-purpose return value pattern


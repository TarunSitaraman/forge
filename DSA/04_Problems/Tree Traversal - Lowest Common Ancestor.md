---
type: problem
status: stable
tags: [dsa/problem, dsa/tree-traversal]
canonical: true
related: [[[Tree Traversal]], [[DFS]], [[Python - DFS Recursive]]]
difficulty: medium
---
# Tree Traversal - Lowest Common Ancestor

## Problem Statement
Given a binary tree and two nodes p and q, find their lowest common ancestor (LCA) — the deepest node that has both p and q as descendants (a node can be a descendant of itself).

**Constraints:**
- Both p and q exist in the tree
- Tree may not be a BST (general binary tree)
- Return the actual node, not just its value

## Classification
- **Patterns:** [[Tree Traversal]], [[DFS]]
- **Template:** [[Python - DFS Recursive]]
- **Category:** Recursive tree query with combined subtree results

## Pattern Selection
This is canonical for the "combine left/right results" DFS pattern:
1. Recursively search left and right subtrees for p and q
2. If found in both subtrees, current node is LCA
3. If found in only one subtree, that subtree's result propagates up
4. Elegant O(n) solution without explicit path tracking

## Why This Approach Works
**Invariant:** If a node's left subtree contains one target and right subtree contains the other, that node IS the LCA.

**Key Insight:**
- Function returns: the node itself if it matches p or q, OR the LCA if both found below, OR whichever target was found (propagate up), OR None if neither found

## Python Solution
```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def lowestCommonAncestor(root: TreeNode, p: TreeNode, q: TreeNode) -> TreeNode:
    """
    Find LCA of p and q in binary tree using recursive DFS.
    Time: O(n) visit each node once
    Space: O(h) recursion depth
    """
    # Base case: null node, or found one of our targets
    if not root or root == p or root == q:
        return root
    
    # Search both subtrees
    left = lowestCommonAncestor(root.left, p, q)
    right = lowestCommonAncestor(root.right, p, q)
    
    # If both subtrees found something, current node is LCA
    if left and right:
        return root
    
    # Otherwise, propagate whichever side found something (or None)
    return left if left else right


def lowestCommonAncestorBST(root: TreeNode, p: TreeNode, q: TreeNode) -> TreeNode:
    """
    Optimized version for BST: use value comparison instead of full search.
    Time: O(h) - only traverse one path
    Space: O(1) iterative, or O(h) if recursive
    """
    node = root
    while node:
        if p.val < node.val and q.val < node.val:
            node = node.left
        elif p.val > node.val and q.val > node.val:
            node = node.right
        else:
            # Split point found: this is the LCA
            return node
    return None
```

## Reasoning
1. **Base Case:** If current node is null or matches p/q, return it immediately
2. **Recurse Both Sides:** Search left and right subtrees independently
3. **Combine Logic:**
   - Both sides non-null → current node is LCA (p and q are in different subtrees)
   - Only one side non-null → that's where both targets are (or one target found so far), propagate up
   - Both null → neither target found in this subtree
4. **BST Optimization:** Use value comparison to navigate directly (no need to search both subtrees)

## Complexity Analysis
- **General Tree:** O(n) time, O(h) space (must potentially visit all nodes)
- **BST Optimized:** O(h) time, O(1) space (only follow one path using comparisons)

## Edge Cases & Validation
```python
# Simple case: p and q in different subtrees
#       3
#      / \
#     5   1
root = TreeNode(3, TreeNode(5), TreeNode(1))
p, q = root.left, root.right
result = lowestCommonAncestor(root, p, q)
assert result == root  # LCA is root itself

# p is ancestor of q
#       3
#      / 
#     5   
#    / \
#   6   2
root = TreeNode(3, TreeNode(5, TreeNode(6), TreeNode(2)))
p = root.left  # node 5
q = root.left.right  # node 2
result = lowestCommonAncestor(root, p, q)
assert result == p  # 5 is ancestor of 2, so 5 is LCA

# p equals q (edge case)
root = TreeNode(1)
result = lowestCommonAncestor(root, root, root)
assert result == root

# Deep tree, targets far apart
#         1
#        / \
#       2   3
#      / \
#     4   5
root = TreeNode(1, TreeNode(2, TreeNode(4), TreeNode(5)), TreeNode(3))
p, q = root.left.left, root.right  # nodes 4 and 3
result = lowestCommonAncestor(root, p, q)
assert result == root
```

## Common Mistakes
1. **Comparing Values Instead of Node References:** 
   - In non-BST trees with potential duplicate values, must compare node identity
   - `root == p` should check object identity, not just `root.val == p.val`

2. **[[Incorrect Base Case]]:** Not handling the case where root itself is p or q
   - Must check `root == p or root == q` before recursing

3. **Wrong Propagation Logic:**
   - Must return `root` when BOTH left and right are truthy
   - Must return whichever side is truthy when only one side found something

4. **BST vs. General Tree Confusion:**
   - BST allows O(h) optimization via value comparison
   - General tree requires full O(n) search since no ordering property exists

## Interview Walkthrough
**Interviewer:** "Find lowest common ancestor of two nodes in binary tree."

**Your Response:**
1. "I'll recursively search left and right subtrees for both targets."
2. "If a node itself matches p or q, I return it immediately—it's a candidate."
3. "If both subtrees return non-null results, current node is the LCA (targets diverge here)."
4. "If only one subtree has results, I propagate that up—the LCA is deeper in that direction."

**Diagram It:**
```
        3
       / \
      5   1
     / \
    6   2
       / \
      7   4

Find LCA(6, 4):
- At node 5: left search finds 6, right search finds 4
- Both non-null → node 5 is LCA!
```

**Why This Resonates:**
- Shows elegant use of recursive return value combining
- Explains propagation logic clearly (not obvious at first glance)
- Demonstrates awareness of BST optimization opportunity

## Variations
1. **LCA in BST:** Simpler using value comparisons (shown above)
2. **LCA with Parent Pointers:** Different approach using two-pointer technique (like linked list intersection)
3. **Distance Between Nodes:** Find LCA, then sum distances from LCA to each node
4. **LCA of Multiple Nodes:** Generalize to find LCA of k nodes, not just 2

## Related Problems
- [[Tree Traversal - Validate BST]] (different tree property validation)
- [[Tree Traversal Representative Problems]]

## Related Concepts
- [[Tree Traversal]] — DFS-based recursive pattern
- [[Binary Search Tree]] — enables O(h) optimization
- [[Recursion]] — combining subtree results elegantly


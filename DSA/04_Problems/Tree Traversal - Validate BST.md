---
type: problem
status: stable
tags: [dsa/problem, dsa/tree-traversal, dsa/bst]
canonical: true
related: [[[Tree Traversal]], [[Binary Search Tree]], [[Python - DFS Recursive]]]
difficulty: medium
---
# Tree Traversal - Validate BST

## Problem Statement
Given a binary tree, determine if it is a valid Binary Search Tree (BST).

A valid BST satisfies: for every node, all values in left subtree are less than node's value, all values in right subtree are greater, and both subtrees are also valid BSTs.

**Constraints:**
- Tree may be empty (valid trivially)
- All node values are unique
- Must validate globally, not just immediate children

## Classification
- **Patterns:** [[Tree Traversal]], [[Recursion]]
- **Template:** [[Python - DFS Recursive]]
- **Category:** Tree validation with range constraints

## Pattern Selection
This is canonical because it demonstrates a common pitfall: checking only immediate parent-child relationship is INSUFFICIENT. Must track valid range (min, max) that narrows as we descend.

## Why Range Tracking Works
**Invariant:** Each node must fall within (min, max) bounds inherited from ancestors.

**Common Mistake to Avoid:**
- WRONG: Only check `node.left.val < node.val < node.right.val`
- This misses cases like: root=5, left=3, right=(4 as left child)
  - 4 < 5 might look fine locally, but 4 must be > 3's whole subtree bound

## Python Solution
```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def isValidBST(root: TreeNode) -> bool:
    """
    Validate BST using range constraints (min, max).
    Time: O(n) visit each node once
    Space: O(h) recursion depth
    """
    def validate(node, low, high):
        if not node:
            return True
        
        # Node must be strictly within (low, high)
        if not (low < node.val < high):
            return False
        
        # Left subtree: same low, but high becomes node.val
        # Right subtree: low becomes node.val, same high
        return (validate(node.left, low, node.val) and
                validate(node.right, node.val, high))
    
    return validate(root, float('-inf'), float('inf'))


def isValidBSTInorder(root: TreeNode) -> bool:
    """
    Alternative: inorder traversal should produce strictly increasing sequence.
    """
    prev = [float('-inf')]  # Use list for closure mutability
    
    def inorder(node):
        if not node:
            return True
        
        if not inorder(node.left):
            return False
        
        if node.val <= prev[0]:
            return False
        prev[0] = node.val
        
        return inorder(node.right)
    
    return inorder(root)
```

## Reasoning
1. **Range Approach:** Pass down valid (low, high) bounds; narrow as we descend
2. **Base Case:** Null node is trivially valid
3. **Check Current:** Node value must be strictly within bounds
4. **Recurse:** Left subtree inherits upper bound = current value; right inherits lower bound = current value
5. **Alternative (Inorder):** BST inorder traversal is sorted; verify strictly increasing

## Complexity Analysis
- **Time:** O(n) — visit each node once
- **Space:** O(h) — recursion stack depth (h = height, worst case O(n) for skewed tree)

## Edge Cases & Validation
```python
# Empty tree
assert isValidBST(None) == True

# Single node
assert isValidBST(TreeNode(1)) == True

# Simple valid BST
root = TreeNode(2, TreeNode(1), TreeNode(3))
assert isValidBST(root) == True

# Classic invalid case (local check would pass, global fails)
#     5
#    / \
#   1   4
#      / \
#     3   6
root = TreeNode(5, TreeNode(1), TreeNode(4, TreeNode(3), TreeNode(6)))
assert isValidBST(root) == False  # 3 < 5 but 3 is in right subtree

# Duplicate values (should fail, BST requires strict inequality)
root = TreeNode(1, TreeNode(1))
assert isValidBST(root) == False
```

## Common Mistakes
1. **[[Incorrect Base Case]]:** Only checking immediate children, not global bounds
2. **Not Using Strict Inequality:** BST typically requires strict `<` and `>`, not `<=`
3. **Integer Overflow Concerns:** Using actual min/max int instead of -infinity/infinity (less issue in Python)
4. **Inorder Prev Tracking:** Using simple variable instead of mutable container in closures

## Interview Walkthrough
**Interviewer:** "Validate if this is a BST."

**Your Response:**
1. "A common mistake is checking only immediate parent-child—that's insufficient."
2. "I'll pass down valid (min, max) range that narrows as I descend the tree."
3. "Alternatively, I can verify inorder traversal produces strictly increasing values."

**Why This Resonates:**
- Shows awareness of the classic pitfall (local vs. global validation)
- Offers two approaches, demonstrating depth of understanding

## Variations
1. **Recover BST:** Two nodes swapped; find and fix them
2. **Convert Sorted Array to BST:** Construction instead of validation
3. **Kth Smallest in BST:** Uses inorder traversal for sorted access

## Related Problems
- [[Tree Traversal Representative Problems]]
- [[DFS - Path Sum]] (different tree DFS application)

## Related Concepts
- [[Tree Traversal]] — inorder property exploitation
- [[Binary Search Tree]] — structural invariant being validated
- [[Recursion]] — range-passing technique


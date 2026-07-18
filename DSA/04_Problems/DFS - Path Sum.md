---
type: problem
status: stable
tags: [dsa/problem, dsa/dfs, dsa/tree]
canonical: true
related: [[[DFS]], [[Tree Traversal]], [[Python - DFS Recursive]]]
difficulty: medium
---
# DFS - Path Sum

## Problem Statement
Given a binary tree and a target sum, determine if the tree has a root-to-leaf path such that adding up all values along the path equals the target sum.

**Constraints:**
- Path must go from root to a leaf (node with no children)
- Path sum is cumulative sum of node values along the path
- Tree may be empty (return False)

## Classification
- **Patterns:** [[DFS]], [[Tree Traversal]]
- **Template:** [[Python - DFS Recursive]]
- **Category:** Root-to-leaf path exploration with running state

## Pattern Selection
This is canonical for DFS with backtracking state because:
1. Requires exploring all root-to-leaf paths
2. Running sum must be tracked and passed down recursion
3. Leaf detection determines when to check termination condition
4. No explicit backtracking needed (sum is passed by value, not mutated in place)

## Why DFS Works
**Invariant:** At each node, remaining target = target - node.val represents what's needed from the rest of the path.

**Strategy:**
- Subtract current node's value from target
- If leaf node reached, check if remaining target equals 0
- Otherwise recurse into children with updated target
- Return True if any path satisfies the condition

## Python Solution
```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def hasPathSum(root: TreeNode, targetSum: int) -> bool:
    """
    Check if root-to-leaf path exists with sum equal to targetSum.
    Time: O(n) visit each node once
    Space: O(h) recursion depth, h = tree height
    """
    if not root:
        return False
    
    # Leaf node: check if remaining sum matches
    if not root.left and not root.right:
        return targetSum == root.val
    
    # Recurse into children with reduced target
    remaining = targetSum - root.val
    return (hasPathSum(root.left, remaining) or 
            hasPathSum(root.right, remaining))


def pathSumAll(root: TreeNode, targetSum: int) -> list[list[int]]:
    """
    Return ALL root-to-leaf paths summing to targetSum.
    Time: O(n^2) worst case (path copying)
    Space: O(n) for result storage
    """
    result = []
    path = []
    
    def dfs(node, remaining):
        if not node:
            return
        
        path.append(node.val)
        remaining -= node.val
        
        # Leaf check
        if not node.left and not node.right and remaining == 0:
            result.append(path[:])  # Copy current path
        else:
            dfs(node.left, remaining)
            dfs(node.right, remaining)
        
        path.pop()  # Backtrack: remove current node from path
    
    dfs(root, targetSum)
    return result
```

## Reasoning
1. **Base Case:** Empty tree has no path, return False
2. **Leaf Check:** If current node is leaf, check if remaining target equals node value
3. **Recursive Case:** Subtract current value from target; recurse into both children
4. **Combine Results:** Return True if EITHER subtree has valid path (OR logic)
5. **Backtracking (for All Paths variant):** Must pop node from path list after exploring both children

## Complexity Analysis
- **Time:** O(n) for single path check — each node visited once
- **Time:** O(n²) for all paths — due to path copying at each leaf
- **Space:** O(h) recursion stack, where h is tree height
  - Balanced tree: O(log n)
  - Skewed tree: O(n)

## Edge Cases & Validation
```python
# Empty tree
assert hasPathSum(None, 5) == False

# Single node matching target
root = TreeNode(5)
assert hasPathSum(root, 5) == True

# Single node not matching
root = TreeNode(3)
assert hasPathSum(root, 5) == False

# Multi-level tree with valid path
#       5
#      / \
#     4   8
#    /   / \
#   11  13  4
#  /  \      \
# 7    2      1
root = TreeNode(5, 
    TreeNode(4, TreeNode(11, TreeNode(7), TreeNode(2))),
    TreeNode(8, TreeNode(13), TreeNode(4, None, TreeNode(1))))
assert hasPathSum(root, 22) == True  # 5+4+11+2 = 22

# Negative values
root = TreeNode(-2, None, TreeNode(-3))
assert hasPathSum(root, -5) == True  # -2 + -3 = -5

# Path exists but not to leaf
root = TreeNode(1, TreeNode(2))
assert hasPathSum(root, 1) == False  # 1 alone isn't leaf path
```

## Common Mistakes
1. **[[Incorrect Base Case]]:** Checking sum at internal node instead of leaf
   - Must verify BOTH children are None (leaf), not just current node's value

2. **[[Boundary Errors]]:** Not distinguishing None node from leaf node
   - `if not root:` handles empty subtree
   - `if not root.left and not root.right:` handles leaf

3. **State Management Errors (All Paths variant):**
   - Forgetting to backtrack (pop from path list)
   - Appending before checking base case (wrong order)

4. **Sum Direction Confusion:**
   - Subtracting from target vs. adding to running sum
   - Both work, but must be consistent

## Interview Walkthrough
**Interviewer:** "Does a root-to-leaf path exist with given sum?"

**Your Response:**
1. "I'll track remaining sum needed as I traverse down the tree."
2. "At each node, I subtract its value from target."
3. "When I reach a leaf, I check if remaining sum equals zero."
4. "I explore left and right subtrees, returning true if either finds a valid path."

**Why This Resonates:**
- Shows understanding of "leaf" vs. "null node" distinction
- Explains state passing (remaining sum) clearly
- Demonstrates OR logic for combining subtree results

## Variations
1. **Path Sum II:** Return all valid paths (requires backtracking with path list)
2. **Path Sum III:** Paths don't need to start at root or end at leaf (any-to-any)
3. **Maximum Path Sum:** Find max sum path (may not include root)
4. **Path Sum with Node Count:** Find path summing to X using exactly K nodes

## Related Problems
- [[DFS - Number of Islands]] (different DFS application: grid vs. tree)
- [[Tree Traversal Representative Problems]]
- [[DFS Representative Problems]]

## Related Concepts
- [[DFS]] — fundamental recursive pattern
- [[Tree Traversal]] — specific structural context
- [[Backtracking]] — required for "All Paths" variant
- [[Recursion]] — passing state through recursive calls


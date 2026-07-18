---
type: problem
status: stable
tags: [dsa/problem, dsa/bfs, dsa/tree]
canonical: true
related: [[[BFS]], [[Tree Traversal]], [[Python - BFS]]]
difficulty: medium
---
# BFS - Level Order Tree Traversal

## Problem Statement
Given a binary tree, return the level order traversal (BFS traversal) of its nodes' values, grouped by level.

**Constraints:**
- Tree may be empty
- Return list of lists, one list per level
- Order within level follows left-to-right

## Classification
- **Patterns:** [[BFS]], [[Tree Traversal]]
- **Template:** [[Python - BFS]]
- **Category:** Level-by-level tree processing

## Pattern Selection
This is canonical for BFS on trees because:
1. Explicitly requires grouping nodes by depth/level
2. Queue naturally maintains FIFO order matching tree levels
3. Level boundaries can be tracked by processing queue size at each iteration
4. No cycle concern (trees are acyclic), so no visited set needed

## Why BFS Works
**Invariant:** All nodes at depth d are dequeued before any node at depth d+1.

**Key Technique - Level Boundary Tracking:**
- At start of each iteration, record current queue size (= nodes at current level)
- Process exactly that many nodes before moving to next level
- Children added during processing belong to next level

## Python Solution
```python
from collections import deque

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def levelOrder(root: TreeNode) -> list[list[int]]:
    """
    Return level-order traversal grouped by level.
    Time: O(n) visit each node once
    Space: O(n) worst case queue size (last level of complete tree)
    """
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        level_size = len(queue)  # Critical: capture size BEFORE processing
        current_level = []
        
        for _ in range(level_size):
            node = queue.popleft()
            current_level.append(node.val)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        result.append(current_level)
    
    return result


def zigzagLevelOrder(root: TreeNode) -> list[list[int]]:
    """
    Level order but alternating left-to-right, right-to-left.
    """
    if not root:
        return []
    
    result = []
    queue = deque([root])
    left_to_right = True
    
    while queue:
        level_size = len(queue)
        current_level = deque()  # Use deque for O(1) append on both ends
        
        for _ in range(level_size):
            node = queue.popleft()
            
            if left_to_right:
                current_level.append(node.val)
            else:
                current_level.appendleft(node.val)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        result.append(list(current_level))
        left_to_right = not left_to_right
    
    return result
```

## Reasoning
1. **Initialize Queue:** Start with root node
2. **Level Boundary Capture:** At loop start, save current queue length (= level size)
3. **Process Exactly Level Size:** Loop that many times, dequeuing each node
4. **Collect Values:** Add each processed node's value to current level list
5. **Enqueue Children:** Add non-null children to queue (they belong to next level)
6. **Append Level:** After processing entire level, add to result

## Complexity Analysis
- **Time:** O(n) — each node processed exactly once
- **Space:** O(n) — worst case, last level of complete binary tree has n/2 nodes

## Edge Cases & Validation
```python
# Empty tree
assert levelOrder(None) == []

# Single node
root = TreeNode(1)
assert levelOrder(root) == [[1]]

# Balanced tree
#     3
#    / \
#   9  20
#     /  \
#    15   7
root = TreeNode(3, TreeNode(9), TreeNode(20, TreeNode(15), TreeNode(7)))
assert levelOrder(root) == [[3], [9, 20], [15, 7]]

# Skewed tree (all left children)
root = TreeNode(1, TreeNode(2, TreeNode(3)))
assert levelOrder(root) == [[1], [2], [3]]

# Zigzag verification
root = TreeNode(3, TreeNode(9), TreeNode(20, TreeNode(15), TreeNode(7)))
assert zigzagLevelOrder(root) == [[3], [20, 9], [15, 7]]
```

## Common Mistakes
1. **[[Boundary Errors]]:** Capturing queue size AFTER adding children (wrong)
   - Must capture `level_size = len(queue)` BEFORE the for loop
   - Otherwise level boundaries blur together

2. **Using List Instead of Deque:**
   - `list.pop(0)` is O(n), making overall complexity O(n²)
   - `deque.popleft()` is O(1), maintaining O(n) overall

3. **Forgetting Null Checks:**
   - Must verify `node.left` and `node.right` exist before enqueueing
   - Missing checks cause AttributeError on None

4. **Zigzag Direction Confusion:**
   - Easy to mix up which levels go left-to-right vs. right-to-left
   - Track boolean flag; toggle after each level

## Interview Walkthrough
**Interviewer:** "Return level-order traversal grouped by level."

**Your Response:**
1. "I'll use BFS with a queue, tracking level boundaries explicitly."
2. "Before processing each level, I capture the current queue size."
3. "I process exactly that many nodes, collecting values into current level."
4. "Children added during processing automatically become next level."

**Why This Resonates:**
- Shows the critical "capture size before loop" technique
- Distinguishes from simple BFS (which doesn't track levels)
- Demonstrates efficient queue usage (deque, not list)

## Variations
1. **Reverse Level Order:** Bottom-up instead of top-down (reverse result at end)
2. **Right Side View:** Return last element of each level (rightmost visible node)
3. **Average of Levels:** Compute average value per level instead of collecting all
4. **Vertical Order Traversal:** Group by column instead of row (requires coordinate tracking)
5. **N-ary Tree Level Order:** Same pattern but nodes have list of children instead of left/right

## Related Problems
- [[BFS - Shortest Path Unweighted]] (similar layer processing, different domain)
- [[Tree Traversal Representative Problems]]
- [[BFS Representative Problems]]

## Related Concepts
- [[BFS]] — fundamental pattern
- [[Tree Traversal]] — broader traversal context
- [[Queue]] — essential FIFO data structure
- [[Deque]] — optimized queue for zigzag variant


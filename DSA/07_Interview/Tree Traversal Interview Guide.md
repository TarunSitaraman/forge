---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/tree-traversal]
canonical: true
related: [[[Tree Traversal]], [[Coding Interviews]], [[Communicating Thought Process]]]
---
# Tree Traversal Interview Guide

## Pattern Recognition (30 seconds)

**Green Flags:**
- Tree structure with parent-child relationships
- Need sorted access (inorder for BST)
- Path-based problems (root-to-leaf, any-to-any)
- Level-based grouping

**Key Decision: Which Traversal?**
| Need | Traversal |
|------|-----------|
| Sorted BST access | Inorder |
| Copy/serialize tree | Preorder |
| Delete tree / postfix expr | Postorder |
| Process by depth/layer | Level-order (BFS) |

## Communication Template

```
Interviewer: "Validate if tree is a valid BST."

You: "BST property means inorder traversal produces sorted sequence.
I'll do inorder traversal, checking that each value is strictly 
greater than the previous one visited."
```

## Core Mechanics

```python
def inorder(node, result):
    if not node:
        return
    inorder(node.left, result)
    result.append(node.val)
    inorder(node.right, result)

def preorder(node, result):
    if not node:
        return
    result.append(node.val)
    preorder(node.left, result)
    preorder(node.right, result)
```

## Common Mistakes

1. **Wrong traversal for problem:** Using preorder when inorder needed for BST validation
2. **Not handling null nodes:** Missing base case causes AttributeError
3. **LCA confusion:** Forgetting that LCA can be one of the two nodes itself
4. **Path state not restored:** Forgetting backtrack in path-sum style problems

## Practice Problems
- Validate BST (inorder)
- Level Order Traversal (BFS)
- Path Sum (DFS with state)
- Lowest Common Ancestor (recursive left/right combination)

## The Checklist
- [ ] Chose correct traversal order for the problem
- [ ] Handled null/leaf base cases
- [ ] Restored path state if backtracking needed
- [ ] Confirmed O(n) time, O(h) space complexity

## Related Content
- [[Tree Traversal]] — Pattern deep dive
- [[Tree Traversal Representative Problems]]


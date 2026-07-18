---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/tree-traversal]
canonical: true
---
# Tree Traversal Representative Problems

Core problem variants that exercise Tree Traversal across different tree types and traversal objectives.

## Variant 1: Inorder Traversal (Sorted Access)
**Recognition:** Access nodes in sorted order (for binary search trees).
**Mechanics:** Recursive: left → process → right. Iterative: stack-based with inorder visiting.
**Key Challenge:** Understanding why inorder gives sorted BST; recognizing when inorder is needed.

- [[Tree Traversal - Inorder BST]]
- Variant: BST to sorted list
- Variant: Validate BST (inorder should be strictly increasing)
- Variant: Kth smallest element (inorder counting)

## Variant 2: Level-Order Traversal (Layers)
**Recognition:** Process tree level by level (breadth-first layer processing).
**Mechanics:** Queue-based; all nodes at depth k before depth k+1.
**Key Challenge:** Distinguishing level boundaries; zigzag variants.

- [[Tree Traversal - Level Order]]
- Variant: Zigzag level order
- Variant: Vertical order (same x-coordinate)
- Variant: View from left/right (first/last node at each level)

## Variant 3: Path Sum & Root-to-Leaf Queries
**Recognition:** Find paths or accumulate values from root to leaves.
**Mechanics:** Recursive with path tracking; backtrack after exploring children.
**Key Challenge:** Path state management; distinguishing leaf nodes.

- [[Tree Traversal - Path Sum]]
- Variant: All root-to-leaf paths
- Variant: Maximum path sum (any node to any node)
- Variant: Diameter (longest path)

## Variant 4: Lowest Common Ancestor (LCA)
**Recognition:** Find deepest common ancestor of two nodes.
**Mechanics:** Recursive: explore left and right; return when finding both nodes.
**Key Challenge:** Handling nodes that don't exist; distinguishing LCA from nodes themselves.

- [[Tree Traversal - Lowest Common Ancestor]]
- Variant: LCA in BST (use value comparison)
- Variant: LCA with parent pointers
- Variant: Distance between nodes (via LCA)

## Variant 5: Binary Search Tree Properties
**Recognition:** Validate, construct, or exploit BST ordering properties.
**Mechanics:** Inorder should be strictly increasing; use min/max validation.
**Key Challenge:** Maintaining valid min/max bounds during recursion.

- [[Tree Traversal - Validate BST]]
- Variant: Recover BST from scrambled node values
- Variant: Convert sorted array to BST
- Variant: Serialize/deserialize BST

## Variant 6: Tree Structure Transformation
**Recognition:** Modify tree structure (flatten, connect siblings, mirror).
**Mechanics:** Recursive tree transformation; careful state management.
**Key Challenge:** In-place modification; maintaining tree integrity.

- Variant: Flatten binary tree to linked list
- Variant: Connect nodes at same level
- Variant: Mirror/invert binary tree
- Variant: Balance unbalanced BST

## Cross-Linking
- **Pattern:** [[Tree Traversal]]
- **Related Patterns:** [[DFS]], [[BFS]], [[Recursion]]
- **Data Structure:** [[Tree]], [[Binary Search Tree]]
- **Templates:** [[Python - DFS Recursive]]
- **Interview Guide:** [[Tree Traversal Interview Guide]]

## Traversal Summary
| Traversal | Order | Use Case |
|-----------|-------|----------|
| **Preorder** | Root → Left → Right | Copy tree, prefix expr |
| **Inorder** | Left → Root → Right | Sorted BST access, validation |
| **Postorder** | Left → Right → Root | Delete tree, postfix expr |
| **Level-order** | Layer by layer | Breadth-first, layers |

## Key Mechanics
1. **Base Case**: Null node or leaf reached?
2. **Order**: Which subtree first (left vs. right)?
3. **Processing**: When to process node (pre/in/post)?
4. **Returning**: What value to return to parent?
5. **State**: Maintain path/sum/parent pointers?

## Common Pitfalls
- Wrong traversal order for problem (inorder vs. preorder)
- Not handling null nodes (NullPointerException)
- Leaf node detection (both children null, or no children?)
- Path state not restored after recursion
- Confusing LCA definition (ancestor vs. node itself)

## Interview Red Flags
- "Validate BST" → Need inorder or min/max tracking
- "Find path with sum" → Need recursive path tracking
- "LCA" → Use recursive helper with (left, right) return pattern
- "Flatten tree" → Postorder typically needed


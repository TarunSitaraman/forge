---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/backtracking]
canonical: true
---
# Backtracking Representative Problems

Core problem variants that exercise Backtracking across different solution spaces and constraint types.

## Variant 1: Permutation Generation
**Recognition:** Generate all permutations with or without constraints.
**Mechanics:** Build permutation incrementally; backtrack when stuck; restore state.
**Key Challenge:** Avoiding duplicates with repeated elements; efficient pruning.

- [[Backtracking - Permutations]]
- Variant: Permutations with duplicates
- Variant: Permutations of specific length

## Variant 2: Combination Selection
**Recognition:** Choose k elements from n without regard to order.
**Mechanics:** Maintain current combination; try next candidate; backtrack.
**Key Challenge:** Avoiding duplicates; boundary tracking in iteration.

- Variant: Combinations with sum constraint
- Variant: Subsets of specific size
- Variant: Partition into k groups

## Variant 3: Constraint Satisfaction (N-Queens Style)
**Recognition:** Place entities satisfying all constraints (position, ownership, etc.).
**Mechanics:** Build solution row/element by row; check constraints; backtrack if violated.
**Key Challenge:** Efficient constraint checking; recognizing invalid states early (pruning).

- [[Backtracking - N-Queens]]
- Variant: Sudoku solver
- Variant: Knight's tour
- Variant: Crossword puzzle

## Variant 4: Path Finding with Restrictions
**Recognition:** Find path(s) from start to end satisfying constraints.
**Mechanics:** Explore all paths recursively; backtrack when hitting obstacle/boundary.
**Key Challenge:** Tracking visited cells; proper backtracking state restoration.

- [[Backtracking - Word Search]]
- Variant: Paths with specific sum
- Variant: Escape maze
- Variant: Restore IP addresses

## Variant 5: Combination Sum with Duplicates
**Recognition:** Find combinations of numbers that sum to target (elements reusable or not).
**Mechanics:** Decide to include/exclude or iterate over remaining choices.
**Key Challenge:** Avoiding duplicate combinations; handling sorted array.

- Variant: Combination sum (each element once)
- Variant: Combination sum II (elements used once, avoid duplicates)
- Variant: Combination sum (elements reusable)

## Variant 6: Partition & Grouping
**Recognition:** Partition set into groups satisfying properties.
**Mechanics:** Assign elements to groups recursively; backtrack when constraint violated.
**Key Challenge:** Symmetry breaking (avoid duplicate partitions); pruning.

- Variant: Partition equal sum subsets
- Variant: Restore spaces/operators
- Variant: Beautiful arrangement

## Cross-Linking
- **Pattern:** [[Backtracking]]
- **Related Patterns:** [[DFS]], [[Recursion]], [[Permutation Generation]]
- **Mistakes:** [[Infinite Loop]], [[Incorrect Base Case]], [[Mutable Defaults]]
- **Interview Guide:** [[Backtracking Interview Guide]]

## Key Mechanics
1. **Decision**: What choice to make at this step?
2. **Explore**: Recursively solve with that choice
3. **Backtrack**: Undo the choice, restore state
4. **Prune**: Detect invalid states early to avoid exploring

## Common Pitfalls
- State not properly restored after recursion
- Duplicate solutions in result
- No pruning (explores too many states)
- Inefficient constraint checking
- Off-by-one in boundary conditions


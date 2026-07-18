---
type: pattern
status: stable
tags: [dsa/pattern, dsa/recursion]
canonical: true
related: [[[Python - DFS Recursive]], [[Python - Tree Traversal]], [[DFS]], [[Backtracking]], [[Divide and Conquer]], [[DFS Cheat Sheet]]]
---
# Recursion

## Intuition
Express a problem in terms of smaller calls with base cases and well-defined return contracts.

## When To Use
Trees, divide and conquer, backtracking, DFS, structural decomposition.

## Theory
The pattern is useful when the problem exposes structure that can be preserved as an invariant. Identify the state that must remain true after each move, then choose operations that make progress without invalidating that state. A correct solution should explain why no skipped state can contain a better answer.

## Reasoning Framework
1. Define the candidate state and what it represents.
2. State the invariant that remains true after each operation.
3. Prove progress toward termination.
4. Validate the boundary cases where the invariant is easiest to break.

## Implementation
Use the canonical implementation link when one exists: [[Python - DFS Recursive]], [[Python - Tree Traversal]]. Keep problem-specific code inside [[Problem Index]] pages and avoid copying full template code here.

## Complexity
Time follows call tree size; stack space follows maximum call depth.

## Tradeoffs
This pattern usually buys asymptotic improvement by using stronger structure. The cost is that correctness depends on preserving the right invariant; if the input does not satisfy the structural assumption, a simpler traversal or dynamic program may be safer.

## Edge Cases
- Empty or single-element input
- Duplicate values or equivalent states
- Inclusive versus exclusive boundaries
- Inputs that barely satisfy the structural assumption

## Common Bugs
[[Recursion Depth]], [[Mutable Defaults]], [[Infinite Loop]]

## Interview Implications
Name the invariant early, justify why each move is safe, and test the smallest counterexample you can imagine. Interviewers often vary these problems by changing boundary semantics, asking for first or last valid positions, or adding duplicate values.

## Practical Engineering Considerations
Prefer readable state names and explicit boundary checks. In production code, document assumptions about ordering, mutability, and failure behavior because these patterns often look correct even when a precondition silently changed.

## Related Algorithms
- [[DFS]], [[Backtracking]], [[Divide and Conquer]]
- [[Algorithm Index]]

## Related Data Structures
- [[Array]]
- [[Graph]]
- [[Tree]]
- [[Hash Map]]

## Python Templates
- [[Python - DFS Recursive]], [[Python - Tree Traversal]]

## Representative Problems
- [[Problem Index]]
- [[Binary Search Representative Problems]]

## Interview Notes
- [[Coding Interviews]]
- [[Communicating Thought Process]]

## Cheat Sheets
- [[DFS Cheat Sheet]]


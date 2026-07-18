---
type: pattern
status: stable
tags: [dsa/pattern, dsa/trie]
canonical: true
related: [[[Python - Trie]], [[DFS]], [[Hashing]], [[Python Collections Cheat Sheet]]]
---
# Trie

## Intuition
Store strings by shared prefixes to support prefix search and incremental character transitions.

## When To Use
Autocomplete, dictionaries, word search, prefix counts, bitwise tries.

## Theory
The pattern is useful when the problem exposes structure that can be preserved as an invariant. Identify the state that must remain true after each move, then choose operations that make progress without invalidating that state. A correct solution should explain why no skipped state can contain a better answer.

## Reasoning Framework
1. Define the candidate state and what it represents.
2. State the invariant that remains true after each operation.
3. Prove progress toward termination.
4. Validate the boundary cases where the invariant is easiest to break.

## Implementation
Use the canonical implementation link when one exists: [[Python - Trie]]. Keep problem-specific code inside [[Problem Index]] pages and avoid copying full template code here.

## Complexity
O(L) per insert/search for key length L; memory depends on node fanout.

## Tradeoffs
This pattern usually buys asymptotic improvement by using stronger structure. The cost is that correctness depends on preserving the right invariant; if the input does not satisfy the structural assumption, a simpler traversal or dynamic program may be safer.

## Edge Cases
- Empty or single-element input
- Duplicate values or equivalent states
- Inclusive versus exclusive boundaries
- Inputs that barely satisfy the structural assumption

## Common Bugs
[[Boundary Errors]]

## Interview Implications
Name the invariant early, justify why each move is safe, and test the smallest counterexample you can imagine. Interviewers often vary these problems by changing boundary semantics, asking for first or last valid positions, or adding duplicate values.

## Practical Engineering Considerations
Prefer readable state names and explicit boundary checks. In production code, document assumptions about ordering, mutability, and failure behavior because these patterns often look correct even when a precondition silently changed.

## Related Algorithms
- [[DFS]], [[Hashing]]
- [[Algorithm Index]]

## Related Data Structures
- [[Array]]
- [[Graph]]
- [[Tree]]
- [[Hash Map]]

## Python Templates
- [[Python - Trie]]

## Representative Problems
- [[Problem Index]]
- [[Binary Search Representative Problems]]

## Interview Notes
- [[Coding Interviews]]
- [[Communicating Thought Process]]

## Cheat Sheets
- [[Python Collections Cheat Sheet]]


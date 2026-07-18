---
type: pattern
status: stable
tags: [dsa/pattern, dsa/prefix-sum]
canonical: true
related: [[[Python - Prefix Sum]], [[Difference Array]], [[Hashing]], [[Complexities Cheat Sheet]]]
---
# Prefix Sum

## Intuition
Precompute cumulative aggregates so range queries become constant-time difference operations.

## When To Use
Repeated range sums, subarray counts, immutable arrays, matrix sums, difference constraints.

## Theory
The pattern is useful when the problem exposes structure that can be preserved as an invariant. Identify the state that must remain true after each move, then choose operations that make progress without invalidating that state. A correct solution should explain why no skipped state can contain a better answer.

## Reasoning Framework
1. Define the candidate state and what it represents.
2. State the invariant that remains true after each operation.
3. Prove progress toward termination.
4. Validate the boundary cases where the invariant is easiest to break.

## Implementation
Use the canonical implementation link when one exists: [[Python - Prefix Sum]]. Keep problem-specific code inside [[Problem Index]] pages and avoid copying full template code here.

## Complexity
O(n) preprocessing and O(1) per one-dimensional range query; space O(n).

## Tradeoffs
This pattern usually buys asymptotic improvement by using stronger structure. The cost is that correctness depends on preserving the right invariant; if the input does not satisfy the structural assumption, a simpler traversal or dynamic program may be safer.

## Edge Cases
- Empty or single-element input
- Duplicate values or equivalent states
- Inclusive versus exclusive boundaries
- Inputs that barely satisfy the structural assumption

## Common Bugs
[[Off-by-One]], [[Boundary Errors]]

## Interview Implications
Name the invariant early, justify why each move is safe, and test the smallest counterexample you can imagine. Interviewers often vary these problems by changing boundary semantics, asking for first or last valid positions, or adding duplicate values.

## Practical Engineering Considerations
Prefer readable state names and explicit boundary checks. In production code, document assumptions about ordering, mutability, and failure behavior because these patterns often look correct even when a precondition silently changed.

## Related Algorithms
- [[Difference Array]], [[Hashing]]
- [[Algorithm Index]]

## Related Data Structures
- [[Array]]
- [[Graph]]
- [[Tree]]
- [[Hash Map]]

## Python Templates
- [[Python - Prefix Sum]]

## Representative Problems
- [[Problem Index]]
- [[Prefix Sum Representative Problems]]

## Interview Notes
- [[Coding Interviews]]
- [[Communicating Thought Process]]

## Cheat Sheets
- [[Complexities Cheat Sheet]]


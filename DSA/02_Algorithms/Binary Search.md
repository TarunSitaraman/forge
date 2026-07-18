---
type: algorithm
status: stable
tags: [dsa/algorithm, dsa/binary-search]
canonical: true
---
# Binary Search

## Overview
Binary Search narrows an ordered or monotonic search space by discarding the half that cannot contain the answer.

## Idea
Choose a midpoint, evaluate the target or predicate, and preserve the invariant that the answer remains in the search range.

## Complexity
- Time: O(log n)
- Space: O(1) iterative, O(log n) recursive

## Implementation
- [[Python - Binary Search]]

## Applications
- Sorted array lookup
- Lower bound / upper bound
- Search on answer
- First true / last false predicates

## Related Patterns
- [[Binary Search]]

## References
- [[Binary Search Cheat Sheet]]
- [[Wrong Mid Calculation]]


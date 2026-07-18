---
type: python-template
status: stable
tags: [dsa/template, python, dsa/backtracking]
canonical: true
related: [[Backtracking]]
---
# Python - Backtracking

## Purpose
Explore candidates recursively while undoing local choices.

## Explanation
This template captures the reusable implementation shape for [[Backtracking]]. Keep problem-specific naming and validation in the problem page, but preserve the invariant and boundary discipline shown here.

## Complexity
Usually exponential; stack space O(depth).

## Code
``python
def subsets(nums):
    result = []
    path = []

    def search(index):
        if index == len(nums):
            result.append(path.copy())
            return
        search(index + 1)
        path.append(nums[index])
        search(index + 1)
        path.pop()

    search(0)
    return result
``

## Edge Cases
- Empty input
- Single-element input
- Duplicate values or repeated states
- Inclusive versus exclusive boundary semantics

## Modifications
Sort first to skip duplicates; add pruning predicates for constraint problems.

## Common Interview Variations
- Return the count instead of the object
- Return all valid objects instead of the best one
- Add online updates or streaming input
- Tighten memory from O(n) to O(1) when dependencies allow it

## Common Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Infinite Loop]]


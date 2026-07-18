---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/recursion]
canonical: true
related: [[[Recursion]], [[Coding Interviews]]]
---
# Recursion Interview Guide

## Pattern Recognition
**Green Flags:** Problem defined in terms of smaller version of itself; tree/graph structure; divide-conquer potential

## Communication Template
`I'll define the base case first (smallest/simplest input), then express the recursive case in terms of smaller subproblems.`

## Core Mechanics
```python
def recursive_solution(n):
    if n <= base_case_threshold:
        return base_case_value
    return combine(recursive_solution(n - 1), additional_work)
```

## Common Mistakes
1. Missing or wrong base case (infinite recursion)
2. Not making progress toward base case
3. Stack overflow on deep recursion (consider iterative or memoization)

## Practice Problems
- Factorial, Fibonacci, Tree Traversal, Merge Sort

## Checklist
- [ ] Base case clearly defined and correct
- [ ] Each recursive call makes progress toward base case
- [ ] Considered iterative alternative if stack depth is concern

## Related
- [[Recursion]]
- [[Recursion Representative Problems]]

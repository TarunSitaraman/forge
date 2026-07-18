---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/memoization]
canonical: true
related: [[[Memoization]], [[Coding Interviews]]]
---
# Memoization Interview Guide

## Pattern Recognition
**Green Flags:** Naive recursion has exponential time due to repeated subproblems; can cache results by input parameters

## Communication Template
`The naive recursive solution recomputes the same subproblems repeatedly. I'll add a cache (dict or array) keyed by input parameters, checking cache before recomputing.`

## Core Mechanics
```python
def fib_memo(n, cache={}):
    if n in cache:
        return cache[n]
    if n <= 1:
        return n
    cache[n] = fib_memo(n-1, cache) + fib_memo(n-2, cache)
    return cache[n]
```

## Common Mistakes
1. Mutable default argument shared across calls (use None default, initialize inside)
2. Cache key doesn't capture full state (missing parameters)
3. Not recognizing when memoization won't help (no repeated subproblems)

## Practice Problems
- Fibonacci, Climbing Stairs, Word Break, Longest Common Subsequence

## Checklist
- [ ] Cache key captures complete relevant state
- [ ] Avoided mutable default argument pitfall
- [ ] Verified overlapping subproblems exist (memoization helps)

## Related
- [[Memoization]]
- [[Memoization Representative Problems]]

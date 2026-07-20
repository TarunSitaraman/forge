---
type: problem
status: stable
tags: [dsa/problem, dsa/backtracking]
canonical: true
related: [[[Backtracking]], [[DFS]], [[Bit Manipulation]]]
difficulty: medium
---
# Backtracking - Subsets (Power Set)

## Problem Statement
Given an array of distinct integers, return all possible subsets (the
power set). Order doesn't matter, but no duplicate subsets.

**Constraints:**
- 1 ≤ nums.length ≤ 10
- All elements distinct
- Must return 2^n subsets total

## Classification
- **Patterns:** [[Backtracking]], [[DFS]]
- **Category:** Include/exclude decision tree

## Pattern Selection
This is the canonical "include or exclude" backtracking template — unlike
permutations (order matters, explore all positions) or combinations
(fixed size), subsets is the purest binary decision at each element: is
it in this subset or not?

## Why This Approach Works
**Key insight:** at each index, there are exactly two choices — include
`nums[i]` in the current subset, or don't. Exploring both branches for
every index naturally generates all 2^n combinations without needing to
track "how many left to pick."

## Python Solution
```python
def subsets(nums: list[int]) -> list[list[int]]:
    """
    Generate all subsets via include/exclude backtracking.
    Time: O(2^n * n) - 2^n subsets, O(n) to copy each
    Space: O(n) recursion depth (excluding output)
    """
    result = []
    current = []

    def backtrack(index):
        if index == len(nums):
            result.append(current[:])
            return

        # Exclude nums[index]
        backtrack(index + 1)

        # Include nums[index]
        current.append(nums[index])
        backtrack(index + 1)
        current.pop()

    backtrack(0)
    return result


def subsetsIterative(nums: list[int]) -> list[list[int]]:
    """
    Alternative: build up subsets iteratively, no recursion.
    Each new number doubles the result set by appending it to every
    existing subset.
    """
    result = [[]]
    for num in nums:
        result += [subset + [num] for subset in result]
    return result


def subsetsBitmask(nums: list[int]) -> list[list[int]]:
    """
    Alternative: each of 2^n integers' bits directly encode a subset.
    """
    n = len(nums)
    result = []
    for mask in range(1 << n):
        subset = [nums[i] for i in range(n) if mask & (1 << i)]
        result.append(subset)
    return result
```

## Reasoning
1. **Base case:** reached the end of the array — current subset is
   complete, save a copy.
2. **Exclude branch first:** recurse without adding `nums[index]`.
3. **Include branch:** add `nums[index]`, recurse, then backtrack (undo)
   before returning — critical so the exclude branch's sibling calls
   aren't corrupted.
4. **Every leaf of this binary decision tree is one valid subset.**

## Complexity Analysis
- **Time:** O(2^n · n) — 2^n subsets, O(n) to copy each into the result
- **Space:** O(n) — recursion depth (excluding output storage)

## Edge Cases & Validation
```python
result = subsets([1,2,3])
assert len(result) == 8  # 2^3
assert [] in result
assert [1,2,3] in result

assert subsets([]) == [[]]  # power set of empty set is {∅}

result_single = subsets([5])
assert sorted(result_single) == sorted([[], [5]])
```

## Common Mistakes
1. **Forgetting to backtrack (pop) after the include branch:** corrupts
   `current` for every subsequent exclude-branch call at a shallower
   recursion depth.
2. **Appending reference instead of copy:** `result.append(current)`
   instead of `current[:]` — all stored "subsets" end up pointing to the
   same mutated list.
3. **Confusing with combinations:** subsets has no target size — every
   length from 0 to n appears; combinations fixes a target `k`.

## Interview Walkthrough
**Interviewer:** "Generate all subsets of this array."

**Your Response:**
1. "At each index, there's a binary choice: include this element or not."
2. "I explore both branches recursively — exclude first, then include."
3. "Base case is reaching the end of the array, where I save whatever
   subset I've built so far."
4. "This naturally produces all 2^n subsets without needing to track a
   target size, unlike combinations."

**Why This Resonates:**
- Shows the include/exclude framing clearly distinguishes subsets from
  permutations/combinations
- Demonstrates awareness of the iterative and bitmask alternatives,
  useful if asked to avoid recursion

## Variations
1. **Subsets II (with duplicates):** sort first, skip duplicate choices
   at the same recursion level to avoid duplicate subsets
2. **Subsets of fixed size k:** becomes [[Backtracking - Combination Sum]]-style, add a size check to the base case
3. **Partition into k equal-sum subsets:** much harder — subset
   generation combined with a sum constraint and search pruning

## Related Problems
- [[Backtracking - Combination Sum]] (fixed target, reusable elements)
- [[Backtracking - N-Queens]] (constraint satisfaction, not free choice)
- [[Bit Manipulation - Single Number]] (bitmask technique used in the
  alternative solution above)
- [[Backtracking Representative Problems]]

## Related Concepts
- [[Backtracking]] — include/exclude decision tree
- [[Bit Manipulation]] — bitmask subset enumeration alternative


---
type: problem
status: stable
tags: [dsa/problem, dsa/meet-in-middle]
canonical: true
related: [[[Meet in the Middle]], [[Hash Map]]]
difficulty: hard
---
# Meet in the Middle - Closest Subset Sum to Target

## Problem Statement
Given an array of up to 40 integers and a target value, find the subset
whose sum is closest to the target (minimize `|sum - target|`).

**Constraints:**
- n up to ~40 — O(2^n) brute force is infeasible, O(2^(n/2)) is fine
- Elements can be positive or negative

## Classification
- **Patterns:** [[Meet in the Middle]]
- **Category:** Split-generate-sort-search for a closest-value query

## Pattern Selection
This extends [[Meet in the Middle - Split Subset Sum]] (which only needs
existence of an *exact* match) to a **closest-value** query — the exact
same split-and-generate setup, but the combine step needs binary search
instead of a hash set, since "closest" requires an ordered structure to
search around.

## Why This Approach Works
**Same split, different combine:** generate all subset sums for each
half (2^(n/2) each), same as the exact-match version. But instead of a
hash set lookup, sort one half's sums and binary-search for the closest
complement to `target - s` for every sum `s` in the other half.

## Python Solution
```python
from bisect import bisect_left

def closestSubsetSum(nums: list[int], target: int) -> int:
    """
    Find subset sum closest to target using meet-in-the-middle.
    Time: O(2^(n/2) * (n/2)) for generation, O(2^(n/2) log(2^(n/2))) for sort+search
    Space: O(2^(n/2))
    """
    n = len(nums)
    half = n // 2
    left, right = nums[:half], nums[half:]

    def all_sums(arr):
        sums = [0]
        for num in arr:
            sums += [s + num for s in sums]
        return sums

    left_sums = all_sums(left)
    right_sums = sorted(all_sums(right))

    best = float('inf')

    for s in left_sums:
        complement = target - s
        idx = bisect_left(right_sums, complement)

        # Check the candidate at idx and idx-1 (closest values on either side)
        for candidate_idx in (idx - 1, idx):
            if 0 <= candidate_idx < len(right_sums):
                total = s + right_sums[candidate_idx]
                best = min(best, abs(total - target))

    return best
```

## Reasoning
1. **Split and generate:** identical to the exact-match version — two
   halves, all subset sums for each, O(2^(n/2)) each.
2. **Sort one half:** enables binary search instead of hash lookup.
3. **For each sum in the other half:** binary search for where its ideal
   complement (`target - s`) would sit in the sorted half, and check the
   neighbors on both sides — the true closest value might be just below
   or just above the search position.
4. **Track the global minimum absolute difference** across all
   left-right pairings considered.

## Complexity Analysis
- **Time:** O(2^(n/2) · log(2^(n/2))) = O(2^(n/2) · n) — generation is
  O(2^(n/2)), sorting is O(2^(n/2) log(2^(n/2))), and n/2 binary
  searches of O(log(2^(n/2))) each
- **Space:** O(2^(n/2)) — storing all subset sums for one half

## Edge Cases & Validation
```python
assert closestSubsetSum([1, 2, 3], 4) == 0        # exact match: 1+3
assert closestSubsetSum([1, 2, 9], 5) == 2         # best is {1,2}=3, |3-5|=2
assert closestSubsetSum([], 5) == 5                # only subset is empty (sum 0)
assert closestSubsetSum([-5, 5], 0) == 0           # -5+5=0
```

## Common Mistakes
1. **Only checking `bisect_left`'s exact index:** the true closest value
   can be at `idx - 1` (just below) — checking only `idx` misses cases
   where the complement falls between two values and the smaller one is
   actually closer.
2. **Reusing the exact-match hash-set approach unmodified:** a hash set
   only answers "does this exact value exist," not "what's the closest
   value" — that fundamentally requires an ordered structure.
3. **Forgetting the empty subset (`sum = 0`)** is always a valid
   candidate in both halves — `all_sums` must start from `[0]`, not an
   empty list.

## Interview Walkthrough
**Interviewer:** "Find the subset sum closest to target, n up to 40."

**Your Response:**
1. "Same setup as exact subset-sum: split into two halves, generate all
   2^(n/2) subset sums for each."
2. "But 'closest' isn't a hash-set question — I need an ordered
   structure, so I sort one half."
3. "For every sum in the other half, I binary-search for its ideal
   complement's position, then check both neighbors — the true closest
   pairing might be just below or just above that position."
4. "This keeps the O(2^(n/2)) complexity class, just with an extra log
   factor for the sort and searches."

## Variations
1. **Exact Subset Sum (existence only):** the simpler sibling problem —
   hash set suffices, no sorting needed
   ([[Meet in the Middle - Split Subset Sum]])
2. **Partition into Two Balanced Subsets:** minimize the difference
   between two subset sums that together use every element — related but
   requires both halves to be fully assigned, not free subsets

## Related Problems
- [[Meet in the Middle - Split Subset Sum]] (exact-match sibling problem)
- [[Meet in the Middle Representative Problems]]

## Related Concepts
- [[Meet in the Middle]] — exponential split technique
- [[Hash Map]] — used in the exact-match sibling instead of binary search


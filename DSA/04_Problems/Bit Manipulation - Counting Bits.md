---
type: problem
status: stable
tags: [dsa/problem, dsa/bit-manipulation, dsa/dynamic-programming]
canonical: true
related: [[[Bit Manipulation]], [[Dynamic Programming]]]
difficulty: easy
---
# Bit Manipulation - Counting Bits

## Problem Statement
Given an integer n, return an array where ans[i] is the number of 1 bits in the binary representation of i, for all 0 ≤ i ≤ n.

**Constraints:**
- Must achieve O(n) time (not O(n log n) via per-number bit counting)

## Classification
- **Patterns:** [[Bit Manipulation]], [[Dynamic Programming]]
- **Category:** Bit counting with DP-based recurrence

## Pattern Selection
This is canonical because naive per-number bit counting is O(n log n), but recognizing the recursive relationship between i and i>>1 (or i and i & (i-1)) achieves O(n).

## Why This Approach Works
**Key Insight (Two Approaches):**
1. **Right Shift Relation:** popcount(i) = popcount(i >> 1) + (i & 1) — remove last bit, add it back if set
2. **Lowest Set Bit Relation:** popcount(i) = popcount(i & (i-1)) + 1 — i & (i-1) removes the lowest set bit

## Python Solution
```python
def countBits(n: int) -> list[int]:
    """
    Count set bits for all numbers 0 to n using DP recurrence.
    Time: O(n)
    Space: O(n) for result array
    """
    result = [0] * (n + 1)
    
    for i in range(1, n + 1):
        # popcount(i) = popcount(i >> 1) + (last bit of i)
        result[i] = result[i >> 1] + (i & 1)
    
    return result


def countBitsAlternative(n: int) -> list[int]:
    """
    Alternative using lowest-set-bit removal relation.
    """
    result = [0] * (n + 1)
    
    for i in range(1, n + 1):
        # i & (i-1) removes lowest set bit
        result[i] = result[i & (i - 1)] + 1
    
    return result
```

## Reasoning
1. **Base Case:** result[0] = 0 (no bits set)
2. **Recurrence (Right Shift):** result[i] = result[i>>1] + (i&1) — shifting right by 1 removes the last bit; add it back if it was 1
3. **Recurrence (Lowest Bit):** result[i] = result[i & (i-1)] + 1 — i&(i-1) clears the lowest set bit, so answer is 1 more than that

## Complexity Analysis
- **Time:** O(n) — single pass, O(1) work per number using precomputed smaller results
- **Space:** O(n) — result array (required for output anyway)

## Edge Cases & Validation
```python
assert countBits(0) == [0]
assert countBits(2) == [0,1,1]
assert countBits(5) == [0,1,1,2,1,2]
```

## Common Mistakes
1. **Naive Per-Number Counting:** O(n log n) using bin(i).count('1') for each i, missing the O(n) DP opportunity
2. **Wrong Recurrence:** Confusing i>>1 relationship (removes last bit) with i&(i-1) relationship (removes lowest set bit)—both work but shouldn't be mixed incorrectly

## Interview Walkthrough
**Interviewer:** "Count set bits for all numbers 0 to n, efficiently."

**Your Response:**
1. "Naive approach counts bits for each number independently—O(n log n)."
2. "Better: use DP recurrence. result[i] = result[i>>1] + (i&1)—removing the last bit is a smaller subproblem."
3. "Alternative: result[i] = result[i & (i-1)] + 1, using the trick that i&(i-1) clears the lowest set bit."
4. "Both achieve O(n) by building on previously computed smaller results."

## Related Problems
- [[Bit Manipulation - Single Number]] (different XOR-based technique)
- [[Bit Manipulation Representative Problems]]

## Related Concepts
- [[Bit Manipulation]] — core bit operations
- [[Dynamic Programming]] — recurrence relation building on smaller subproblems


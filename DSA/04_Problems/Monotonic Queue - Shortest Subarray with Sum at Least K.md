---
type: problem
status: stable
tags: [dsa/problem, dsa/monotonic-queue, dsa/prefix-sum]
canonical: true
related: [[[Monotonic Queue]], [[Prefix Sum]], [[Sliding Window]]]
difficulty: hard
---
# Monotonic Queue - Shortest Subarray with Sum at Least K

## Problem Statement
Given an integer array (which **may contain negative numbers**) and an
integer `k`, find the length of the shortest contiguous subarray with
sum ≥ k. Return -1 if no such subarray exists.

**Constraints:**
- Array may contain negative numbers — this is what breaks the simple
  sliding-window approach
- 1 ≤ nums.length ≤ 10^5

## Classification
- **Patterns:** [[Monotonic Queue]], [[Prefix Sum]]
- **Category:** Prefix sums combined with a monotonic deque, not a plain
  sliding window

## Pattern Selection
This is the canonical example of **why plain sliding window fails** once
negative numbers are allowed, and how combining [[Prefix Sum]] with a
monotonic deque recovers an O(n) solution.

## Why Plain Sliding Window Doesn't Work Here
With all-positive numbers, growing the window only increases the sum, so
a simple two-pointer window works (see
[[Sliding Window - Longest Substring Without Repeating Characters]]-style
mechanics). With negatives allowed, sum is **not monotonic** as the
window grows — shrinking from the left might *increase* the sum if the
dropped element was negative. A pure two-pointer window can't safely
decide when to shrink.

## Why the Monotonic Deque + Prefix Sum Approach Works
**Key insight:** work with prefix sums `P[0..n]`. The subarray
`nums[i..j-1]` has sum `P[j] - P[i]`. We want the smallest `j - i` such
that `P[j] - P[i] >= k`.

Maintain a deque of indices with **increasing prefix sum values**:
- **Pop from the front** while `P[j] - P[deque[0]] >= k` — this index is
  a valid left boundary, and since we're scanning `j` left to right, this
  is the *best* (leftmost, hence shortest with this `j`) valid pairing
  we'll ever get for this front index, so record it and discard it.
- **Pop from the back** while `P[deque[-1]] >= P[j]` — any future
  right-boundary would rather pair with the smaller, more recent
  `P[j]` than this larger, older one, so the old one can never be
  useful again.

## Python Solution
```python
from collections import deque

def shortestSubarray(nums: list[int], k: int) -> int:
    """
    Find shortest subarray with sum >= k, allowing negative numbers.
    Time: O(n) - each index pushed and popped from the deque once
    Space: O(n) for prefix sums and deque
    """
    n = len(nums)
    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i + 1] = prefix[i] + nums[i]

    dq = deque()  # indices into prefix[], increasing prefix-sum order
    best = n + 1

    for j in range(n + 1):
        # Front: this is a valid, shortest-so-far pairing for index j
        while dq and prefix[j] - prefix[dq[0]] >= k:
            best = min(best, j - dq.popleft())

        # Back: maintain increasing prefix sum order
        while dq and prefix[dq[-1]] >= prefix[j]:
            dq.pop()

        dq.append(j)

    return best if best <= n else -1
```

## Reasoning
1. **Build prefix sums:** `prefix[j] - prefix[i]` gives the sum of
   `nums[i..j-1]`.
2. **Scan `j` left to right**, maintaining a deque of candidate left
   boundaries with strictly increasing prefix sums.
3. **Front-pop when valid:** if the current `j` combined with the
   front's index already satisfies `>= k`, record the length and discard
   that front — no later `j` could ever produce a *shorter* valid
   subarray using that same left boundary.
4. **Back-pop when dominated:** if the back of the deque has a prefix
   sum ≥ the current one, it can never be a better left boundary than
   the current index (same or better sum, and closer to future `j`s), so
   discard it.

## Complexity Analysis
- **Time:** O(n) — each index enters and leaves the deque at most once
- **Space:** O(n) — prefix sum array and deque

## Edge Cases & Validation
```python
assert shortestSubarray([1], 1) == 1
assert shortestSubarray([1,2], 4) == -1
assert shortestSubarray([2,-1,2], 3) == 3
assert shortestSubarray([84,-37,32,40,95], 167) == 3
```

## Common Mistakes
1. **Applying plain two-pointer sliding window directly:** fails silently
   on inputs with negative numbers — this is precisely the trap this
   problem is designed to test.
2. **Popping from the back with the wrong comparison:** must be `>=`
   (not `>`) — if two prefix sums are equal, the more recent (larger)
   index is still strictly better as a future left boundary since it
   yields a shorter subarray length.
3. **Not realizing the front-pop condition can fire multiple times per
   `j`:** must be a `while`, not an `if` — several old left boundaries
   might all become valid (and get superseded) at the same `j`.

## Interview Walkthrough
**Interviewer:** "Find the shortest subarray with sum at least k. The
array can have negative numbers."

**Your Response:**
1. "With negatives allowed, plain sliding window breaks — shrinking the
   window doesn't monotonically change the sum, so I can't safely decide
   when to shrink."
2. "Instead, I'll work with prefix sums and a monotonic deque of
   candidate left-boundary indices, kept in increasing prefix-sum
   order."
3. "For each right boundary, I pop valid pairings off the front — the
   moment the deque's smallest prefix sum. also makes a valid pair, it's
   the shortest possible with that left boundary."
4. "I also pop from the back whenever an old index's prefix sum is no
   better than the current one — it can never be useful again."
5. "This achieves O(n) despite negatives breaking the simpler window
   approach."

## Variations
1. **Maximum Sum Subarray (no length constraint, Kadane's):** simpler —
   no deque needed since it's a pure running-max problem
2. **Sliding Window Maximum:** the "vanilla" monotonic queue problem this
   one builds on — fixed window size, no prefix sums involved
   ([[Monotonic Queue - Sliding Window Maximum]])

## Related Problems
- [[Monotonic Queue - Sliding Window Maximum]] (foundational monotonic
  deque mechanics without the prefix-sum layer)
- [[Prefix Sum - Subarray Sum Equals K]] (related prefix-sum technique,
  all-positive-friendly hash-map approach instead)
- [[Monotonic Queue Representative Problems]]

## Related Concepts
- [[Monotonic Queue]] — deque-based candidate pruning
- [[Prefix Sum]] — reduces subarray sum to a difference of two prefix
  values
- [[Sliding Window]] — the simpler technique that fails here, worth
  contrasting explicitly


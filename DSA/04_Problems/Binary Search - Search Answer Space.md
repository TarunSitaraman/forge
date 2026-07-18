---
type: problem
status: stable
tags: [dsa/problem, dsa/binary-search]
canonical: true
related: [[[Binary Search]], [[Python - Binary Search]]]
difficulty: hard
---
# Binary Search - Search Answer Space (Koko Eating Bananas)

## Problem Statement
Koko loves bananas. There are `n` piles of bananas. Koko can eat at most `k` bananas per hour from a single pile. If a pile has fewer than `k` bananas, she eats them all and moves to next pile (doesn't eat from another pile that hour). Find the minimum `k` such that Koko can eat all bananas within `h` hours.

**Constraints:**
- 1 ≤ piles.length ≤ 10^4
- piles.length ≤ h ≤ 10^9
- 1 ≤ piles[i] ≤ 10^9

## Classification
- **Patterns:** [[Binary Search]]
- **Template:** [[Python - Binary Search]]
- **Category:** Binary search on answer space (not on array indices)

## Pattern Selection
This is canonical for "Binary Search on Answer" because:
1. We're not searching for a value IN an array, but searching for optimal PARAMETER value
2. The answer space (possible k values) has monotonic property: if k works, any k' > k also works
3. Can binary search on k directly, checking feasibility at each candidate
4. Classic transformation: "minimize k such that condition holds" → binary search

## Why Binary Search Works
**Invariant:** The feasibility function (can Koko finish in h hours with speed k) is monotonic in k.

**Key Insight - Monotonicity:**
- If speed k allows finishing within h hours, any speed k' > k also allows finishing (or faster)
- If speed k does NOT allow finishing within h hours, any speed k' < k also fails
- This monotonic property is what makes binary search applicable

**Search Space:**
- Lower bound: k=1 (slowest possible, still valid to try)
- Upper bound: k=max(piles) (fastest reasonable, eats largest pile in 1 hour)

## Python Solution
```python
import math

def minEatingSpeed(piles: list[int], h: int) -> int:
    """
    Find minimum eating speed using binary search on answer space.
    Time: O(n log(max(piles))) - n for feasibility check, log(max) for binary search
    Space: O(1)
    """
    def hoursNeeded(speed: int) -> int:
        """Calculate total hours needed at given eating speed."""
        total_hours = 0
        for pile in piles:
            # Ceiling division: hours to finish this pile
            total_hours += math.ceil(pile / speed)
        return total_hours
    
    lo, hi = 1, max(piles)
    
    while lo < hi:
        mid = lo + (hi - lo) // 2
        
        if hoursNeeded(mid) <= h:
            # This speed works; try slower (smaller) speed
            hi = mid
        else:
            # This speed too slow; need faster (larger) speed
            lo = mid + 1
    
    return lo


def minEatingSpeedAlternative(piles: list[int], h: int) -> int:
    """
    Alternative using explicit binary search boundaries.
    """
    def canFinish(speed: int) -> bool:
        hours = sum((pile + speed - 1) // speed for pile in piles)  # Ceiling division
        return hours <= h
    
    lo, hi = 1, max(piles)
    result = hi
    
    while lo <= hi:
        mid = lo + (hi - lo) // 2
        
        if canFinish(mid):
            result = mid  # This works, but try smaller
            hi = mid - 1
        else:
            lo = mid + 1  # Doesn't work, need larger
    
    return result
```

## Reasoning
1. **Define Feasibility Function:** `hoursNeeded(speed)` calculates hours required at given speed
2. **Identify Search Space:** Speed ranges from 1 (slowest) to max(piles) (fastest useful speed)
3. **Binary Search Setup:** lo=1, hi=max(piles)
4. **Feasibility Check:** At each mid, check if hoursNeeded(mid) <= h
5. **Narrow Search Space:**
   - If feasible, this speed works; try smaller speed (hi = mid)
   - If not feasible, need faster speed (lo = mid + 1)
6. **Convergence:** When lo == hi, we've found minimum feasible speed

## Complexity Analysis
- **Time:** O(n log(max(piles)))
  - Binary search iterations: O(log(max(piles)))
  - Each iteration's feasibility check: O(n) to sum hours across all piles
  - Total: O(n log(max(piles)))

- **Space:** O(1)
  - Only using pointer variables and accumulator

## Edge Cases & Validation
```python
# Simple case
assert minEatingSpeed([3,6,7,11], 8) == 4

# Exact fit (h equals number of piles)
assert minEatingSpeed([30,11,23,4,20], 5) == 30

# More hours than piles (slower speed sufficient)
assert minEatingSpeed([30,11,23,4,20], 6) == 23

# Single pile
assert minEatingSpeed([5], 1) == 5
assert minEatingSpeed([5], 5) == 1

# Large pile, few hours
assert minEatingSpeed([1000000000], 2) == 500000000

# Multiple small piles
assert minEatingSpeed([1,1,1,1], 4) == 1
```

## Common Mistakes
1. **[[Wrong Mid Calculation]]:** Off-by-one in binary search boundaries
   - Using `hi = mid` vs `hi = mid - 1` matters based on inclusive/exclusive semantics
   - Must be consistent with loop condition (`lo < hi` vs `lo <= hi`)

2. **Integer Division Instead of Ceiling:**
   - `pile / speed` gives floor, but we need CEILING (partial pile still takes full hour)
   - Correct: `math.ceil(pile / speed)` or `(pile + speed - 1) // speed`

3. **[[Boundary Errors]]:** Incorrect search space bounds
   - Lower bound should be 1 (not 0, since speed must eat at least 1 banana/hour)
   - Upper bound should be max(piles) (eating fastest single pile in one hour)

4. **Not Recognizing Answer Space Search:**
   - Trying to apply binary search directly on piles array (wrong)
   - Must recognize we're searching on SPEED values, not array indices

## Interview Walkthrough
**Interviewer:** "Find minimum eating speed to finish all bananas in h hours."

**Your Response:**
1. "This isn't searching within an array—I'm searching for an optimal SPEED value."
2. "Key insight: if speed k works, any faster speed k+1 also works (monotonic property)."
3. "I'll binary search on speed, checking feasibility (can finish in h hours) at each candidate."
4. "For each candidate speed, I calculate total hours needed using ceiling division."

**Why This Resonates:**
- Shows recognition of "binary search on answer" pattern (not obvious from problem statement)
- Explains the crucial monotonicity property that enables binary search
- Demonstrates correct handling of ceiling division for partial piles

## Variations
1. **Ship Packages Within Days:** Similar structure—minimize capacity given days
2. **Split Array Largest Sum:** Minimize the maximum subarray sum given k splits
3. **Minimum Speed to Arrive on Time:** Similar answer-space binary search
4. **Capacity to Ship Packages:** Nearly identical pattern with different domain

## Interview Recognition Signal
**When to think "Binary Search on Answer":**
- Problem asks to "minimize" or "maximize" some parameter
- There's a clear feasibility check function
- The feasibility function has monotonic behavior (if X works, X+1 or X-1 also works in same direction)

## Related Problems
- [[Binary Search - Rotated Sorted Array]] (different binary search adaptation)
- [[Binary Search Representative Problems]]

## Related Concepts
- [[Binary Search]] — foundational pattern
- [[Monotonic Functions]] — property enabling this technique
- [[Ceiling Division]] — critical computational detail


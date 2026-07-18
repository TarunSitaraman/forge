---
type: problem
status: stable
tags: [dsa/problem, dsa/prefix-sum, dsa/hash-map]
canonical: true
related: [[[Prefix Sum]], [[Hash Map]], [[Python - Prefix Sum]]]
difficulty: medium
---
# Prefix Sum - Subarray Sum Equals K

## Problem Statement
Given an array of integers and a target sum k, find the **total number of subarrays** whose sum equals k.

**Constraints:**
- Subarray must be contiguous
- Count distinct subarrays (position matters)
- Array can contain negative numbers

## Classification
- **Patterns:** [[Prefix Sum]], [[Hash Map]]
- **Template:** [[Python - Prefix Sum]]
- **Category:** Exact sum query using prefix sums and hash map

## Pattern Selection
This is canonical for Prefix Sum + Hash Map because:
1. Naive O(n²) checks all subarrays; prefix sums make it O(n)
2. Hash map enables O(1) lookup of "how many subarrays ending here have sum k?"
3. Key insight: `prefix[j] - prefix[i-1] = k` ⟹ `prefix[i-1] = prefix[j] - k`
4. Instead of checking all prior indices, look up count in hash map

## Why Prefix Sum + Hash Map Works
**Invariant:** For each position j, count how many prior positions i have `prefix[i] = prefix[j] - k`.

**Insight:**
- Compute running prefix sum as we scan right
- For each new element, check: "How many times have I seen prefix_sum - k?"
- Each occurrence represents a complete subarray [i+1, j] with sum k

**Why It Works:**
- If `prefix[j] - prefix[i] = k`, then `sum(array[i+1:j+1]) = k`
- By tracking all prior prefix values, we count all valid subarrays ending at j
- We never miss one because every position's prefix is recorded

## Python Solution
```python
def subarray_sum(nums: list[int], k: int) -> int:
    """
    Count subarrays with sum equal to k.
    Time: O(n) single pass with hash map
    Space: O(n) for prefix sum storage
    """
    # Map from prefix_sum to count of occurrences
    prefix_count = {0: 1}  # Initialize with 0:1 for subarrays starting at index 0
    current_sum = 0
    count = 0
    
    for num in nums:
        current_sum += num
        
        # How many subarrays ending here have sum k?
        # If prefix[i] = current_sum - k, then sum from i+1 to current = k
        if current_sum - k in prefix_count:
            count += prefix_count[current_sum - k]
        
        # Record this prefix sum for future lookups
        prefix_count[current_sum] = prefix_count.get(current_sum, 0) + 1
    
    return count
```

## Reasoning
1. **Initialize:** `prefix_count = {0: 1}` (base case for subarrays starting at 0)
2. **Iterate:** Scan through array, building running sum
3. **Lookup:** For each position, check how many prior prefixes equal `current_sum - k`
   - Each match represents a valid subarray with sum k
4. **Record:** Store current prefix in map for future queries
5. **Return:** Total count of subarrays with sum k

## Complexity Analysis
- **Time:** O(n)
  - Single pass through array: n iterations
  - Each hash map lookup/insert: O(1) average
  - Total: O(n)
- **Space:** O(n)
  - Hash map stores at most n unique prefix sums
  - In worst case (all different prefixes): O(n)

## Edge Cases & Validation
```python
# Single element equals k
assert subarray_sum([1], 1) == 1

# Single element doesn't equal k  
assert subarray_sum([1], 2) == 0

# Multiple subarrays with same sum
assert subarray_sum([1, 1, 1], 2) == 2  # [1,1] at positions (0,1) and (1,2)

# Negative numbers
assert subarray_sum([1, -1, 1, 1], 1) == 3  # [1], [1,-1,1], [1,1]

# Overlapping subarrays
assert subarray_sum([1, 2, 1], 3) == 2  # [1,2] and [2,1]

# All zeros
assert subarray_sum([0, 0, 0], 0) == 6  # Six subarrays: [0], [0,0], [0,0,0], etc.

# k equals 0 with negative numbers
assert subarray_sum([-1, 1, -1, 1], 0) == 5
```

## Common Mistakes
1. **Forgetting `{0: 1}` Initialization:**
   - Without this, subarrays starting at index 0 are missed
   - `{0: 1}` handles: "How many subarrays starting at 0 have sum k?" ✓

2. **Using `append` Instead of Count:**
   - Wrong: Store all prefix values in a list, then count occurrences
   - Right: Store counts directly in hash map for O(1) lookup

3. **[[Off-by-One]]**: 
   - Checking `current_sum == k` instead of `current_sum - k in counts`
   - The latter finds all subarrays, not just prefix-from-start

4. **Updating Prefix Before Lookup:**
   - Wrong: `counts[current_sum] += 1` before checking
   - Right: Check first, then update (avoid double-counting current element)

5. **Not Handling Duplicates:**
   - Multiple subarrays can have the same sum
   - Hash map must store **count**, not just boolean presence

## Interview Walkthrough
**Interviewer:** "Count subarrays with sum k."

**Your Response:**
1. "If I check all O(n²) subarrays, it's too slow."
2. "I'll use prefix sums: if prefix[j] - prefix[i] = k, then subarray [i+1, j] has sum k."
3. "Instead of checking all i for each j, I use a hash map to count prefix[j] - k occurrences."
4. "This reduces to O(n) single pass, where each position lookups prior prefixes."

**Diagram It:**
```
Array:    [1, 2, 1]
k = 3

Iteration 1: sum=1, sum-k=-2, count {0:1, 1:1} → 0 matches
Iteration 2: sum=3, sum-k=0, found 1 match {0:1} → answer += 1
           → count {0:1, 1:1, 3:1}
Iteration 3: sum=4, sum-k=1, found 1 match {1:1} → answer += 1
           → count {0:1, 1:1, 3:1, 4:1}

Answer: 2 ✓ (subarrays [1,2] and [2,1])
```

**Why This Resonates:**
- Shows reduction from O(n²) brute-force to O(n) elegant solution
- Explains prefix sum math clearly: why `prefix[i] = prefix[j] - k` works
- Demonstrates hash map as optimization tool, not just data structure

## Variations
1. **Subarray with Sum At Most k:**
   - Can't use same approach (need sliding window or binary search on sorted prefixes)

2. **Subarrays with Sum in Range [a, b]:**
   - Count prefixes in range [current_sum - b, current_sum - a]
   - Requires sorted container (TreeMap, SortedList)

3. **Divisible by k Instead of Equal to k:**
   - Change `current_sum - k` to focus on modular arithmetic
   - Use remainders instead of exact values

4. **Maximum Subarray Sum (Kadane's Algorithm):**
   - Related but different approach (doesn't use counting)

## Interview Prep Notes
- **Whiteboard:** Draw prefix sum array and hash map evolution
- **Proof Question:** "Why doesn't updating prefix before checking cause issues?"
- **Complexity Justification:** Why O(n) and not O(n log n)?
- **Variation Drill:** "At most k", "in range [a,b]", "divisible by k"

## Related Problems
- [[Prefix Sum Representative Problems]]
- [[Prefix Sum - 2D Matrix Range Sum]] (2D variant)
- [[Sliding Window - Longest Substring With At Most K Distinct Characters]] (similar hash map usage)

## Related Concepts
- [[Prefix Sum]] — foundation of range sum queries
- [[Hash Map]] — O(1) lookup for counting
- [[Time Complexity]] — O(n²) brute-force vs O(n) optimized
- [[Cumulative Sum]] — understanding prefix sum relationships


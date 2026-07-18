---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/prefix-sum]
canonical: true
---
# Prefix Sum Representative Problems

Core problem variants that exercise the Prefix Sum pattern across different sum-based constraints and scenarios.

## Variant 1: Exact Sum Query
**Recognition:** Find subarrays with sum equal to target k.
**Mechanics:** Compute prefix sums; use hash map to track (prefix_sum - k) occurrences.
**Key Challenge:** Why does counting prefix_sum - k give you the answer?

- [[Prefix Sum - Subarray Sum Equals K]]
- Variant: Count subarrays with sum divisible by k
- Variant: Subarrays with given sum

## Variant 2: Sum Comparison (At Most / At Least)
**Recognition:** Find subarrays where sum satisfies inequality constraint.
**Mechanics:** Prefix sums enable range [i, j] sum query in O(1).
**Key Challenge:** Combine with sliding window or binary search on prefix array.

- [[Prefix Sum - Maximum Subarray Sum]]
- Variant: Minimum subarray sum
- Variant: Subarrays with sum at most k

## Variant 3: 2D Prefix Sum (Matrix Operations)
**Recognition:** Compute rectangular sums in 2D matrix efficiently.
**Mechanics:** Build 2D prefix sum array; query in O(1) using inclusion-exclusion.
**Key Challenge:** Inclusion-exclusion formula: four terms to combine correctly.

- [[Prefix Sum - 2D Matrix Range Sum]]
- Variant: Matrix sum queries with updates (Fenwick Tree variant)

## Variant 4: Modular Arithmetic
**Recognition:** Find subarrays where sum % k has specific property.
**Mechanics:** Prefix sums mod k; track first occurrence of each remainder.
**Key Challenge:** Remainder cycles; handle negative modulo correctly.

- Variant: Subarrays with sum divisible by k
- Variant: Subarrays with sum % k = specific value

## Variant 5: Coordinate Difference (Difference Array Inverse)
**Recognition:** Find ranges where cumulative changes satisfy condition.
**Mechanics:** Prefix sum of difference array gives cumulative effect.
**Key Challenge:** Relationship between difference array and prefix sums.

- Variant: When does array exceed threshold
- Variant: Queries on range updates

## Cross-Linking
- **Pattern:** [[Prefix Sum]]
- **Related:** [[Difference Array]] (inverse operation)
- **Template:** [[Python - Prefix Sum]]
- **Mistakes:** [[Off-by-One]], [[Boundary Errors]]
- **Cheat Sheet:** [[Prefix Sum Cheat Sheet]]

## Interview Insights
- **Recognition Pattern:** "Subarray" + "sum" + "constraint" → likely prefix sum
- **Common Ask:** "Can you do this in one pass?" (hash map + prefix sum enables it)
- **Variant Pressure:** Interviewers often add:
  - 2D arrays (matrix operations)
  - Modular arithmetic (sum % k)
  - Range updates (difference array)
- **Proof Question:** "Why does prefix[j] - prefix[i-1] give the sum of [i, j]?"

## Key Mechanics
1. **Build Phase:** Compute prefix sums from left to right
2. **Query Phase:** Use prefix sums to answer subarray sum in O(1)
3. **Hash Map Tracking:** Store occurrences of each prefix sum for exact match queries
4. **Boundary Handling:** Include prefix[0] = 0 for subarray starting at index 0

## Time Complexity Pattern
- **Build:** O(n) to compute all prefix sums
- **Query:** O(1) per range query using prefix array, or O(n) with hash map
- **Total:** Usually O(n) for complete solution

## Related Patterns
- [[Sliding Window]]: Can combine with prefix sums for certain constraints
- [[Difference Array]]: Inverse of prefix sum; used for range updates
- [[Hash Map]]: Track prefix sums for exact match queries

## Common Implementation Pattern
```python
# Build prefix sum array
prefix = [0]
for num in array:
    prefix.append(prefix[-1] + num)

# Query range sum [i, j] in O(1)
range_sum = prefix[j+1] - prefix[i]

# For exact match queries: track prefix in hash map
sums = {0: 1}  # prefix sum -> count
for num in array:
    prefix_sum += num
    if target - prefix_sum in sums:
        count += sums[target - prefix_sum]
    sums[prefix_sum] = sums.get(prefix_sum, 0) + 1
```


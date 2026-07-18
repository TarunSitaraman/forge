---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/difference-array]
canonical: true
---
# Difference Array Representative Problems

Core problem variants that exercise Difference Array pattern for efficient range updates and queries.

## Variant 1: Range Update & Query
**Recognition:** Apply multiple range updates efficiently; later query final state.
**Mechanics:** Mark range [i, j] with +1 at i and -1 at j+1; compute prefix sum.
**Key Challenge:** Handling boundaries (i and j+1); understanding why this works.

- [[Difference Array - Range Updates]]
- Variant: Add to all elements in range
- Variant: Subtract from range
- Variant: Multiple updates then single query

## Variant 2: Increment/Decrement by Value
**Recognition:** Different ranges increment/decrement by different amounts.
**Mechanics:** Mark difference values; compute prefix sum; final array has actual values.
**Key Challenge:** Non-unit increments; maintaining value differences.

- Variant: Range sum queries after updates
- Variant: Seats rearrangement
- Variant: Minimum operations to make array equal

## Variant 3: Conflict Detection
**Recognition:** Detect conflicts when multiple changes overlap.
**Mechanics:** Use difference array to track cumulative changes; check if any exceeds threshold.
**Key Challenge:** Handling overlapping ranges; detecting conflicts early.

- Variant: Booking conflict detection
- Variant: Resource allocation validation
- Variant: Capacity constraints

## Variant 4: Distance & Arrival Problems
**Recognition:** Track distances or arrival patterns over ranges.
**Mechanics:** Difference array tracks changes in value across positions.
**Key Challenge:** Understanding problem's semantics in difference array terms.

- Variant: Can arrive on time (delivery/timing)
- Variant: Shortest path in weighted grid
- Variant: Distance tracking

## Variant 5: Offline Processing
**Recognition:** Process all queries/updates together rather than online.
**Mechanics:** Difference array enables O(n+m) solution vs. O(n*m) online.
**Key Challenge:** Recognizing problems suitable for offline approach.

- Variant: Range minimum query offline
- Variant: Largest rectangle in matrix (offline)
- Variant: Regions separated by slashes (offline reconstruction)

## Variant 6: Coordinate Compression
**Recognition:** Compress large coordinate space before difference array application.
**Mechanics:** Map large values to smaller range; apply difference array; map back.
**Key Challenge:** Maintaining correctness after coordinate compression.

- Variant: Merge intervals (coordinate compression variant)
- Variant: Overlapping rectangles area
- Variant: Range assignment

## Cross-Linking
- **Pattern:** [[Difference Array]]
- **Related Pattern:** [[Prefix Sum]] (inverse operation)
- **Algorithms:** Used in range update problems
- **Interview Guide:** [[Difference Array Interview Guide]]

## Why Difference Array?
- ✅ O(1) per range update
- ✅ O(n) final query after all updates
- ✅ Total: O(n+m) for n elements, m updates
- ❌ Online queries need recomputation
- ❌ Single query only: no benefit over direct access

## Common Pattern
```
1. Build difference array: diff[i] = arr[i] - arr[i-1]
2. Apply updates: diff[l] += val, diff[r+1] -= val
3. Compute prefix sum: arr[i] = prefix_sum[i]
```

## Complexity Patterns
- **Per Update:** O(1)
- **After k Updates:** O(k) to mark, O(n) to compute final array
- **Total:** O(n+k)

## Common Pitfalls
- Updating actual array instead of difference array
- Off-by-one in range boundaries (j vs. j+1)
- Forgetting to compute prefix sum (just difference isn't answer)
- Not recognizing offline optimization opportunity

## Interview Red Flags
- "Multiple range updates" → Difference array
- "Merge intervals" → Consider difference array approach
- "Range queries after updates" → Offline difference array


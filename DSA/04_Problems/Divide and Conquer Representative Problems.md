---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/divide-and-conquer]
canonical: true
---
# Divide and Conquer Representative Problems

Core problem variants that exercise Divide and Conquer across different problem decomposition strategies.

## Variant 1: Merge Sort & Inversion Count
**Recognition:** Sort array or count inversions by divide-and-conquer.
**Mechanics:** Divide: split in half. Conquer: sort each half. Combine: merge sorted halves.
**Key Challenge:** Efficient merging; counting inversions during merge.

- [[Divide and Conquer - Merge Sort]]
- Variant: Inversion count (pairs where i < j but arr[i] > arr[j])
- Variant: Merge k sorted lists (generalize merging)

## Variant 2: Quick Sort & Selection
**Recognition:** Sort or find kth element by divide-and-conquer partitioning.
**Mechanics:** Partition: pick pivot, split into <pivot and >pivot. Recursively sort/select.
**Key Challenge:** Pivot selection; handling duplicates; expected O(n log n) time.

- Variant: Quicksort
- Variant: Kth largest (partial sort)
- Variant: Select in expected linear time

## Variant 3: Binary Search Variants
**Recognition:** Find target in sorted array or answer space via divide-and-conquer.
**Mechanics:** Compare middle with target; eliminate half; recurse.
**Key Challenge:** Boundary handling; recognizing binary search within larger problem.

- Variant: Binary search on answer (search space is possible values)
- Variant: Search in rotated sorted array
- Variant: Find peak element

## Variant 4: Closest Pair Problem
**Recognition:** Find two closest points in 2D space or similar geometric problem.
**Mechanics:** Divide by x-coordinate; conquer each half; combine by checking strip.
**Key Challenge:** Strip checking; avoiding O(n²) in combine phase.

- [[Divide and Conquer - Closest Pair]]
- Variant: Closest distance between points
- Variant: Maximum gap in sorted array

## Variant 5: Counting Problems
**Recognition:** Count objects satisfying property via divide-and-conquer.
**Mechanics:** Count in each half; count across boundary; sum.
**Key Challenge:** Efficiently counting across divide point; avoiding double-count.

- Variant: Inversion count
- Variant: Smaller elements after self
- Variant: Count of range sum subset

## Variant 6: Recursive Decomposition
**Recognition:** Break complex problem into smaller subproblems; solve independently.
**Mechanics:** Define base case; divide into subproblems; combine results.
**Key Challenge:** Identifying correct subproblem structure; ensuring progress to base case.

- Variant: Matrix chain multiplication
- Variant: Power function (fast exponentiation)
- Variant: Majority element

## Cross-Linking
- **Pattern:** [[Divide and Conquer]]
- **Related Patterns:** [[Recursion]], [[Dynamic Programming]]
- **Algorithms:** [[Merge Sort]], [[Quick Sort]], [[Binary Search]]
- **Interview Guide:** [[Divide and Conquer Interview Guide]]

## Key Steps
1. **Divide:** Break into subproblems of smaller size
2. **Conquer:** Solve subproblems recursively
3. **Combine:** Merge solutions from subproblems
4. **Base Case:** Termination condition

## Complexity Pattern
T(n) = a·T(n/b) + O(n^d)
- If a > b^d: O(n^log_b(a))
- If a = b^d: O(n^d log n)
- If a < b^d: O(n^d)

## Common Pitfalls
- Infinite recursion (no progress toward base case)
- Overlapping subproblems (should use DP instead)
- Expensive combine phase (defeats divide-and-conquer benefit)
- Unbalanced divide (partition isn't roughly equal)


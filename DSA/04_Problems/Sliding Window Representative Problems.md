---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/sliding-window]
canonical: true
---
# Sliding Window Representative Problems

Core problem variants that exercise the Sliding Window pattern across different constraint scenarios and data types.

## Variant 1: Fixed-Size Window
**Recognition:** Find optimal value across all contiguous subarrays of fixed length k.
**Mechanics:** Slide window forward; add new element to the right, remove from the left.
**Key Challenge:** Efficiently update state when element leaves and enters window.

- [[Sliding Window - Maximum in Sliding Window]]
- Variant: Minimum in sliding window
- Variant: Average in sliding window (pre-compute suffix)
- Variant: Median in sliding window (requires balanced structure)

## Variant 2: Constraint on Element Frequency
**Recognition:** Find longest/shortest substring where each character appears at most k times.
**Mechanics:** Expand window right; contract from left when constraint violated.
**Key Challenge:** When constraint fails, how much to shrink (one step vs. multiple)?

- [[Sliding Window - Longest Substring With At Most K Distinct Characters]]
- Variant: Longest substring with all unique characters
- Variant: Longest repeating character replacement (vary all characters up to k times)
- Variant: Permutation substring (frequency vector matching)

## Variant 3: Target Sum / Subarray Sum
**Recognition:** Find contiguous subarray satisfying sum constraint (equals, at least, at most).
**Mechanics:** Expand for candidates; contract when constraint violated.
**Key Challenge:** Decision to contract: one element at a time? Jump directly?

- [[Sliding Window - Subarray Sum Equals K]]
- Variant: Subarray sum at most k (minimum length, maximum length variants)
- Variant: Number of subarrays with sum exactly k (requires prefix sum map)

## Variant 4: Two-Pointer Subarray Search
**Recognition:** Find subarray satisfying predicate (min length, max product, etc.).
**Mechanics:** Left boundary contracts only when constraint satisfied; right boundary expands.
**Key Challenge:** Predicate changes position of contraction point.

- [[Sliding Window - Minimum Window Substring]]
- Variant: Minimum window subsequence (non-contiguous requirement)
- Variant: Minimum operations to make all window elements equal

## Variant 5: Frequency Matching
**Recognition:** Find substring containing all characters from target (possibly duplicated).
**Mechanics:** Track frequency of characters; window valid when target frequencies met.
**Key Challenge:** Efficient frequency comparison and state updates.

- [[Sliding Window - Permutation in String]]
- Variant: Minimum window substring (generalization)
- Variant: Anagram substring

## Cross-Linking
- **Pattern:** [[Sliding Window]]
- **Template:** [[Python - Sliding Window]]
- **Mistakes:** [[Off-by-One]], [[Boundary Errors]], [[Infinite Loop]]
- **Cheat Sheet:** [[Sliding Window Cheat Sheet]]
- **Interview Guide:** [[Sliding Window Interview Guide]]

## Interview Insights
- **Recognition Pattern:** "Contiguous" + "subarray/substring" + "optimal/constraint" → likely sliding window
- **Common Ask:** "Find the minimum/maximum length satisfying condition"
- **Variant Pressure:** Interviewers often change:
  - Window size (fixed vs. variable)
  - Constraint type (frequency vs. sum vs. predicate)
  - Return value (length, count, actual subarray, indices)
- **Proof Question:** "When can you safely move the left boundary?"

## Key Mechanics to Nail
1. **Initialization:** Where do pointers start?
2. **Expansion:** When and how to move right boundary?
3. **Contraction:** When can left boundary safely move?
4. **State Update:** How to efficiently track window state?
5. **Termination:** When is answer final?

## Time Complexity Pattern
Most sliding window solutions achieve **O(n)** because:
- Right pointer moves through array once (n movements)
- Left pointer moves through array once (n movements)
- Total: 2n = O(n)

Avoid: O(n²) by re-computing window state from scratch each iteration.

## Related Patterns
- [[Two Pointers]]: Similar bidirectional movement, different invariants
- [[Prefix Sum]]: Can be combined with sliding window for sum constraints
- [[Hash Map]]: Used to track frequencies in current window


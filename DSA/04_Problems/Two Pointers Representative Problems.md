---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/two-pointers]
canonical: true
---
# Two Pointers Representative Problems

Core problem variants that exercise the Two Pointers pattern across different structural scenarios.

## Variant 1: Sorted Array Pair Sum
**Recognition:** Find pairs in a sorted array meeting a target condition.
**Mechanics:** Pointers converge from opposite ends; move based on comparison.
**Key Challenge:** Why does inward movement never skip valid pairs?

- [[Two Pointers - Two Sum Sorted]]
- Variant: All unique pairs summing to target
- Variant: Triple sum (3Sum)
- Variant: 4Sum and beyond

## Variant 2: Maximizing/Minimizing Between Pointers
**Recognition:** Optimize a quantity (area, distance) bounded by two positions.
**Mechanics:** Decide which boundary to move based on current state.
**Key Challenge:** Greedy boundary choice is safe without exhaustive search.

- [[Two Pointers - Container With Most Water]]
- Variant: Trapping rain water (requires height matching)
- Variant: Best time to buy/sell stock (temporal variant)

## Variant 3: In-Place Array Modification
**Recognition:** Reorganize array structure without extra space using two pointers.
**Mechanics:** Write pointer tracks positions to overwrite; read pointer scans forward.
**Key Challenge:** Invariant preservation when pointers cross or overlap.

- [[Two Pointers - Remove Duplicates]]
- Variant: Remove element (mark positions to skip)
- Variant: Sort colors (partition into 3 regions)
- Variant: Move zeros to end (in-place permutation)

## Variant 4: Subarray/Subsequence at Opposite Ends
**Recognition:** Validate or construct sequences by matching endpoints.
**Mechanics:** Converge or diverge based on character/value match.
**Key Challenge:** Order of operations when structural constraints change.

- [[Two Pointers - Valid Palindrome]]
- Variant: Merge sorted lists (interleave at boundaries)
- Variant: Shortest palindrome (pattern matching variant)

## Variant 5: Merging Two Sorted Sequences
**Recognition:** Combine two ordered sequences into one, usually in-place.
**Mechanics:** Start from end of larger container; pull from two sources.
**Key Challenge:** Working backward prevents overwriting unprocessed data.

- [[Two Pointers - Merge Sorted Array]]
- Variant: Merge sorted linked lists (pointer-based list operation)

## Cross-Linking
- **Pattern:** [[Two Pointers]]
- **Template:** [[Python - Two Pointers]]
- **Mistakes:** [[Off-by-One]], [[Boundary Errors]], [[Infinite Loop]]
- **Cheat Sheet:** [[Python Collections Cheat Sheet]]
- **Interview Guide:** [[Two Pointers Interview Guide]]

## Interview Insights
- **Common Ask:** "Can you solve this in-place with O(1) space?"
- **Variant Pressure:** Interviewers often add constraints (modify in-place, no extra structures, handle duplicates)
- **Proof Question:** "Why does moving this boundary guarantee we don't skip a valid answer?"


---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/monotonic-stack]
canonical: true
---
# Monotonic Stack Representative Problems

Core problem variants that exercise Monotonic Stack pattern across different "next/previous greater/smaller" and span problems.

## Variant 1: Next Greater Element
**Recognition:** For each element, find the next larger element to the right.
**Mechanics:** Stack keeps indices in decreasing order; pop when finding larger element.
**Key Challenge:** Handling no-greater-element case; proper index management.

- [[Monotonic Stack - Next Greater Element]]
- Variant: Next greater element (circular)
- Variant: Next greater element (in different array)
- Variant: Daily temperatures

## Variant 2: Previous/Smaller Elements
**Recognition:** For each element, find previous smaller or next smaller element.
**Mechanics:** Monotonic stack in increasing order; pop for smaller elements.
**Key Challenge:** Distinguishing next vs. previous; smaller vs. greater.

- Variant: Previous smaller element
- Variant: Next smaller element
- Variant: Trapping rain water (two pointers alternative)

## Variant 3: Stock Span Problem
**Recognition:** For each day, count consecutive days with price ≤ today (span).
**Mechanics:** Monotonic decreasing stack; span = current_index - stack_top_index.
**Key Challenge:** Handling multiple equal prices; off-by-one in span calculation.

- [[Monotonic Stack - Stock Span]]
- Variant: Largest rectangle (histogram variant)
- Variant: Sum of subarray minimums

## Variant 4: Largest Rectangle in Histogram
**Recognition:** Find largest rectangular area in histogram.
**Mechanics:** Monotonic increasing stack of bars; calculate area when popping.
**Key Challenge:** Understanding "span" of each bar; proper area calculation.

- [[Monotonic Stack - Largest Rectangle]]
- Variant: Maximal rectangle in matrix (extend histogram idea)
- Variant: Sum of subarray minimums (similar mechanics)

## Variant 5: Validating Sequences & Removal
**Recognition:** Validate or modify sequences based on element ordering properties.
**Mechanics:** Stack builds valid subsequence; remove elements that violate monotonicity.
**Key Challenge:** Greedy removal decision; ensuring removed elements don't repeat.

- [[Monotonic Stack - Remove K Digits]]
- Variant: Lexicographically smallest subsequence
- Variant: Smallest number without repeating (string variant)

## Variant 6: Competitive Analysis
**Recognition:** Analyze relationships between elements (e.g., stronger person behind, weaker eliminated).
**Mechanics:** Process in order; stack tracks relevant elements; pop when condition met.
**Key Challenge:** Understanding problem's ordering and elimination rules.

- Variant: Online stock span
- Variant: 132 pattern (find i < j < k where nums[i] < nums[k] < nums[j])
- Variant: Evaluate reverse polish notation (stack but not monotonic)

## Cross-Linking
- **Pattern:** [[Monotonic Stack]]
- **Related Patterns:** [[Stack]], [[Array]], [[Greedy]]
- **Similar Ideas:** [[Sliding Window]], [[Two Pointers]]
- **Interview Guide:** [[Monotonic Stack Interview Guide]]

## Key Insight
The monotonic stack efficiently answers:
- "What's the next element larger than me?"
- "What's the previous element smaller than me?"
- "What's my span (consecutive elements within range)?"

In O(n) time by processing each element once (amortized).

## Common Patterns
| Problem Type | Stack Order | Pops When |
|---|---|---|
| **Next Greater** | Decreasing | Element > top |
| **Previous Smaller** | Increasing | Element < top |
| **Spans/Histogram** | Decreasing | Element >= top |

## Common Pitfalls
- Using wrong monotonicity (increasing vs. decreasing)
- Forgetting to push element after popping others
- Off-by-one in index calculations
- Not handling empty stack when popping
- Confusing next/previous and greater/smaller directions

## Interview Red Flags
- "Next greater element" → Classic monotonic stack
- "Daily temperatures" → Same as next greater
- "Largest rectangle" → Histogram with monotonic stack
- "Lexicographically smallest" → Greedy removal with stack
- "Eliminate redundant X" → Monotonic stack often applies

## Why Monotonic Stack?
- ✅ O(n) time (each element pushed/popped once)
- ✅ O(n) space (stack size bounded by monotonic property)
- ❌ Not obvious from problem statement (recognition is key)
- ❌ Index management can be tricky


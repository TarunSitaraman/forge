---
type: problem
status: stable
tags: [dsa/problem, dsa/monotonic-stack, dsa/greedy]
canonical: true
related: [[[Monotonic Stack]], [[Greedy]]]
difficulty: medium
---
# Monotonic Stack - Remove K Digits

## Problem Statement
Given a non-negative integer as a string `num` and an integer `k`,
remove `k` digits from `num` to make the smallest possible number
(as a string, no leading zeros, unless the result is "0").

**Constraints:**
- 1 ≤ k ≤ num.length
- Result must not have leading zeros (except the single digit "0")
- If removing all digits, result is "0"

## Classification
- **Patterns:** [[Monotonic Stack]], [[Greedy]]
- **Category:** Greedy digit removal via monotonic increasing stack

## Pattern Selection
This is canonical for combining monotonic stack mechanics with a greedy
removal decision: to minimize the resulting number, a digit should be
removed if a **smaller** digit immediately follows it — removing a larger
digit before a smaller one always helps, which is exactly the "pop while
top is greater than incoming" monotonic-stack pattern.

## Why This Approach Works
**Greedy insight:** in the smallest possible number, digits should be as
small as possible in the most significant positions. If `num[i] >
num[i+1]`, keeping `num[i]` can never be optimal — removing it strictly
improves (or doesn't hurt) the result, since a smaller digit now sits in
that more significant position.

**Stack mechanics:** maintain an increasing stack of digits. When the
next digit is smaller than the stack's top, pop (i.e., "remove" that
larger digit) as long as removals remain (`k > 0`).

## Python Solution
```python
def removeKdigits(num: str, k: int) -> str:
    """
    Remove k digits to form the smallest possible number, using a
    monotonic increasing stack.
    Time: O(n) - each digit pushed and popped at most once
    Space: O(n) for the stack
    """
    stack = []

    for digit in num:
        while k > 0 and stack and stack[-1] > digit:
            stack.pop()
            k -= 1
        stack.append(digit)

    # If digits remain to remove (num was non-decreasing), trim from the end
    while k > 0:
        stack.pop()
        k -= 1

    result = ''.join(stack).lstrip('0')
    return result if result else "0"
```

## Reasoning
1. **Scan left to right, maintain increasing stack:** each digit is a
   candidate; while it's smaller than the stack's top and removals
   remain, pop the top (removing the larger, more significant digit).
2. **Push current digit:** after any necessary pops.
3. **Leftover removals:** if `k` never hit zero during the scan (the
   string was already non-decreasing), the largest remaining digits are
   at the *end* of the stack — trim from there.
4. **Clean leading zeros:** strip them, but preserve a single `"0"` if
   everything was stripped.

## Complexity Analysis
- **Time:** O(n) — each digit pushed once and popped at most once
  (amortized, standard monotonic stack argument)
- **Space:** O(n) — stack holds up to n digits

## Edge Cases & Validation
```python
assert removeKdigits("1432219", 3) == "1219"
assert removeKdigits("10200", 1) == "200"       # leading zero stripped
assert removeKdigits("10", 2) == "0"            # remove everything
assert removeKdigits("112", 1) == "11"          # non-decreasing input
assert removeKdigits("9", 1) == "0"
```

## Common Mistakes
1. **Forgetting the leftover-removal step:** if `num` is already
   non-decreasing (e.g., "112"), the while-loop inside the scan never
   triggers, and without trimming from the end afterward, too many
   digits remain.
2. **Not stripping leading zeros:** "10200" with k=1 naively gives
   "0200" instead of "200".
3. **Returning empty string instead of "0"** when every digit gets
   removed or stripped as leading zeros.
4. **Popping without checking `k > 0`:** would remove more digits than
   allowed, or (if bounds-unchecked) run past the actual removal budget.

## Interview Walkthrough
**Interviewer:** "Remove k digits to make the smallest possible number."

**Your Response:**
1. "To minimize the number, I want smaller digits in more significant
   (leftmost) positions."
2. "So whenever the next digit is smaller than what's already on my
   stack, I should pop the larger digit — that's a monotonic increasing
   stack pattern."
3. "I only pop while I still have removals left (`k > 0`)."
4. "Two edge cases to handle after the scan: if I haven't used up all my
   removals (input was non-decreasing), trim from the end of the stack;
   and strip any resulting leading zeros, falling back to '0' if
   everything's gone."

## Variations
1. **Create Maximum Number:** similar monotonic stack but maximizing
   instead of minimizing — pop when top is *smaller* than incoming digit
2. **Remove Duplicate Letters (lexicographically smallest, each letter
   once):** adds a "last occurrence" constraint on top of the same
   monotonic stack idea
3. **132 Pattern:** different application of monotonic stack — detecting
   a specific triple pattern rather than removal

## Related Problems
- [[Monotonic Stack - Next Greater Element]] (foundational mechanics)
- [[Monotonic Stack - Largest Rectangle]] (different use of the same
  increasing-stack structure)
- [[Monotonic Stack Representative Problems]]

## Related Concepts
- [[Monotonic Stack]] — increasing-stack removal pattern
- [[Greedy]] — "remove larger digit before smaller one" local choice


---
type: problem
status: stable
tags: [dsa/problem, dsa/sliding-window, dsa/hash-map]
canonical: true
related: [[[Sliding Window]], [[Hash Map]]]
difficulty: medium
---
# Sliding Window - Permutation in String

## Problem Statement
Given strings `s1` and `s2`, return true if `s2` contains a permutation
of `s1` as a contiguous substring.

**Constraints:**
- A permutation means same characters, same frequencies, any order
- 1 ≤ s1.length ≤ s2.length ≤ 10^4

## Classification
- **Patterns:** [[Sliding Window]], [[Hash Map]]
- **Category:** Fixed-size window with frequency matching

## Pattern Selection
This is canonical for **fixed-size** sliding window (unlike variable-size
problems like [[Sliding Window - Minimum Window Substring]]): the window
is always exactly `len(s1)`, and the question at each position is purely
"does this window's character frequency match s1's frequency?"

## Why Sliding Window Works
**Key insight:** instead of re-counting frequencies from scratch at every
window position (O(n·k)), maintain a running frequency difference that
updates in O(1) per slide — add the new right character, remove the
departing left character.

## Python Solution
```python
from collections import Counter

def checkInclusion(s1: str, s2: str) -> bool:
    """
    Check if s2 contains a permutation of s1 using fixed-size window.
    Time: O(n) where n = len(s2)
    Space: O(1) - bounded by alphabet size (26 lowercase letters)
    """
    if len(s1) > len(s2):
        return False

    need = Counter(s1)
    window = Counter(s2[:len(s1)])

    if window == need:
        return True

    for i in range(len(s1), len(s2)):
        # Slide: add s2[i], remove s2[i - len(s1)]
        window[s2[i]] += 1
        left_char = s2[i - len(s1)]
        window[left_char] -= 1
        if window[left_char] == 0:
            del window[left_char]

        if window == need:
            return True

    return False
```

## Reasoning
1. **Fixed window size:** exactly `len(s1)`, established once up front.
2. **Initial window:** count frequencies of `s2`'s first `len(s1)` chars.
3. **Slide by exactly one each step:** add the new right character,
   remove the character falling out the left — O(1) amortized update.
4. **Match check:** compare the window's frequency map to `s1`'s target
   frequency map each time the window shifts.

## Complexity Analysis
- **Time:** O(n) — each character enters and leaves the window exactly
  once, O(1) work per slide (comparing two bounded-size Counters is
  O(26) = O(1))
- **Space:** O(1) — frequency maps bounded by the 26-letter alphabet

## Edge Cases & Validation
```python
assert checkInclusion("ab", "eidbaooo") == True   # "ba" is a permutation of "ab"
assert checkInclusion("ab", "eidboaoo") == False
assert checkInclusion("adc", "dcda") == True       # "dca" or "cda"
assert checkInclusion("abc", "ab") == False        # s1 longer than s2
assert checkInclusion("a", "a") == True
```

## Common Mistakes
1. **Recomputing full frequency count every slide:** O(n·k) instead of
   O(n) — misses the incremental update opportunity.
2. **Comparing Counters incorrectly:** forgetting that `Counter`
   equality already handles "same keys, same counts" — no need to
   manually iterate.
3. **Not deleting zero-count entries:** if using a plain dict instead of
   Counter, a key present with value 0 breaks equality checks against a
   target map that never had that key.

## Interview Walkthrough
**Interviewer:** "Does s2 contain a permutation of s1?"

**Your Response:**
1. "A permutation just means matching character frequencies — order
   doesn't matter."
2. "So I need a fixed-size window of length `len(s1)` sliding through
   `s2`, checking if its frequency map matches s1's at each position."
3. "Instead of recounting every window from scratch, I update
   incrementally: add the incoming character, remove the outgoing one."
4. "This gives O(n) instead of O(n * len(s1))."

## Variations
1. **Find All Anagram Start Indices:** same mechanics, collect every
   matching position instead of returning at the first match
2. **Minimum Window Substring:** the variable-size generalization —
   window must *contain* target frequencies, not *exactly equal* them
3. **Longest Substring with At Most K Distinct:** different constraint
   type (count of distinct chars, not exact frequency match)

## Related Problems
- [[Sliding Window - Minimum Window Substring]] (variable-size sibling
  problem)
- [[Sliding Window - Longest Substring Without Repeating Characters]]
- [[Sliding Window Representative Problems]]

## Related Concepts
- [[Sliding Window]] — fixed-size window variant
- [[Hash Map]] — frequency tracking via Counter


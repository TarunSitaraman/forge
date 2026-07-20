---
type: problem
status: stable
tags: [dsa/problem, dsa/string-matching, dsa/kmp]
canonical: true
related: [[[String Matching]], [[KMP]]]
difficulty: easy
---
# String Matching - Repeated Substring Pattern

## Problem Statement
Given a string `s`, determine if it can be constructed by taking a
substring of it and repeating it multiple times (at least twice).

**Constraints:**
- 1 ≤ s.length ≤ 10^4
- Must be efficient — O(n) or O(n log n), not brute-force O(n^3)

## Classification
- **Patterns:** [[String Matching]]
- **Category:** KMP failure-function reuse for periodicity detection

## Pattern Selection
This is a canonical, slightly surprising application of the **KMP failure
function** beyond pattern searching: the failure function's last value
directly encodes the string's longest proper prefix-that's-also-a-suffix,
which turns out to reveal the string's fundamental period in O(1)
additional work once computed.

## Why the KMP Failure Function Reveals Periodicity
**Key fact:** let `f = failure[n-1]` (the failure value at the last
character, using the standard KMP failure array). If `n % (n - f) == 0`
and `n - f < n`, then the string is exactly `(n - f)` characters repeated
`n / (n - f)` times — no need for the substring to be reconstructed and
checked; `n - f` *is* the period length.

**Intuition:** the failure function measures the longest border (prefix
that equals a suffix) of the whole string. If the string is genuinely
periodic with period `p`, the string's failure value at the end is
exactly `n - p` — the border overlap left after removing one period's
worth of characters.

## Python Solution
```python
def repeatedSubstringPattern(s: str) -> bool:
    """
    Detect if s is built from a repeated substring using the KMP
    failure function's periodicity property.
    Time: O(n) - one pass to build the failure function
    Space: O(n) for the failure array
    """
    n = len(s)
    failure = [0] * n
    j = 0

    for i in range(1, n):
        while j > 0 and s[i] != s[j]:
            j = failure[j - 1]
        if s[i] == s[j]:
            j += 1
        failure[i] = j

    period = n - failure[n - 1]
    return period < n and n % period == 0


def repeatedSubstringPatternBruteForce(s: str) -> bool:
    """
    Simpler but less elegant: try every possible period length.
    Time: O(n^2) in the worst case (string concatenation cost per try)
    """
    n = len(s)
    for length in range(1, n // 2 + 1):
        if n % length == 0:
            if s[:length] * (n // length) == s:
                return True
    return False
```

## Reasoning
1. **Build the KMP failure array** exactly as for pattern matching — no
   special modification needed, same O(n) construction.
2. **Read the periodicity signal off the last value:** `n - failure[-1]`
   is the candidate period length.
3. **Validate:** the string is truly built from repetition only if that
   candidate period evenly divides `n` (and isn't the whole string
   itself, `period < n`).

## Complexity Analysis
- **Time:** O(n) — single KMP failure-function pass
- **Space:** O(n) — failure array

## Edge Cases & Validation
```python
assert repeatedSubstringPattern("abab") == True     # "ab" repeated twice
assert repeatedSubstringPattern("aba") == False
assert repeatedSubstringPattern("abcabcabcabc") == True
assert repeatedSubstringPattern("a") == False       # single char, no repetition
assert repeatedSubstringPattern("aa") == True       # "a" repeated twice
```

## Common Mistakes
1. **Brute-forcing all substring lengths with string multiplication:**
   works and is easy to reason about (shown above as the alternative),
   but is less impressive in an interview if the KMP insight is expected
   — and can degrade toward O(n²) with large inputs given Python string
   concatenation costs.
2. **Forgetting `period < n`:** without this check, a string with no
   internal repetition (failure value 0) computes `period = n`, and
   `n % n == 0` trivially — must explicitly exclude "the whole string is
   its own only period."
3. **Misreading the failure function's meaning:** it's easy to assume it
   directly gives "is this periodic" as a boolean rather than needing the
   `n - failure[-1]` transformation to extract the actual period length.

## Interview Walkthrough
**Interviewer:** "Can this string be built by repeating a substring?"

**Your Response:**
1. "Brute force would try every possible substring length as a
   candidate period — O(n²) or worse with string building costs."
2. "Instead, I can reuse the KMP failure function — its final value
   encodes the string's longest prefix that's also a suffix."
3. "The gap between the string length and that value, `n - failure[-1]`,
   is exactly the string's fundamental period length if one exists."
4. "I just need to check that period evenly divides the total length,
   and isn't the whole string itself."

## Variations
1. **Find the Index of the First Occurrence (strStr):** the "normal" use
   of the KMP failure function — pattern searching rather than
   self-periodicity ([[String Matching - Implement strStr]])
2. **Shortest Palindrome:** another clever repurposing of KMP, matching
   the string against its own reverse to find the minimum prefix to add

## Related Problems
- [[String Matching - Implement strStr]] (the failure function's
  standard use case, worth comparing directly against this repurposing)
- [[String Matching Representative Problems]]

## Related Concepts
- [[String Matching]] — broader pattern-matching context
- [[KMP]] — the algorithm whose failure function is repurposed here


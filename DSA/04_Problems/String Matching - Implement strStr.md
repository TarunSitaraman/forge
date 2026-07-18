---
type: problem
status: stable
tags: [dsa/problem, dsa/string-matching]
canonical: true
related: [[[String Matching]], [[KMP]]]
difficulty: easy
---
# String Matching - Implement strStr() (KMP)

## Problem Statement
Given two strings needle and haystack, return the index of the first occurrence of needle in haystack, or -1 if not found.

**Constraints:**
- Must beat naive O(n*m) for large inputs
- Empty needle returns 0

## Classification
- **Patterns:** [[String Matching]]
- **Category:** Efficient pattern matching via KMP

## Pattern Selection
This is canonical for String Matching because KMP demonstrates the failure function technique for avoiding redundant character comparisons after a mismatch.

## Why KMP Works
**Key Insight:** Precompute a "failure function" that tells us, upon mismatch, how far back in the pattern we can safely resume matching without re-checking characters we know already match.

## Python Solution
```python
def strStr(haystack: str, needle: str) -> int:
    """
    Find first occurrence of needle in haystack using KMP.
    Time: O(n+m) where n=len(haystack), m=len(needle)
    Space: O(m) for failure function
    """
    if not needle:
        return 0
    
    def buildFailure(pattern):
        failure = [0] * len(pattern)
        j = 0
        for i in range(1, len(pattern)):
            while j > 0 and pattern[i] != pattern[j]:
                j = failure[j-1]
            if pattern[i] == pattern[j]:
                j += 1
            failure[i] = j
        return failure
    
    failure = buildFailure(needle)
    j = 0
    
    for i in range(len(haystack)):
        while j > 0 and haystack[i] != needle[j]:
            j = failure[j-1]
        if haystack[i] == needle[j]:
            j += 1
        if j == len(needle):
            return i - j + 1  # Match found
    
    return -1
```

## Reasoning
1. **Failure Function:** For each position in pattern, compute longest proper prefix that's also a suffix
2. **Matching Loop:** Compare haystack and pattern; on mismatch, use failure function to skip redundant checks
3. **Complete Match:** When j reaches pattern length, we've found a match

## Complexity Analysis
- **Time:** O(n+m) — linear in combined length, no backtracking in haystack
- **Space:** O(m) — failure function array

## Edge Cases & Validation
```python
assert strStr("hello", "ll") == 2
assert strStr("aaaaa", "bba") == -1
assert strStr("", "") == 0
assert strStr("a", "") == 0
```

## Common Mistakes
1. **Naive O(n*m) When Large Input Expected:** Missing KMP optimization opportunity
2. **Off-by-One in Failure Function:** Common source of bugs in KMP implementation
3. **Not Handling Empty Needle:** Should return 0 immediately

## Interview Walkthrough
**Interviewer:** "Find first occurrence of pattern in text efficiently."

**Your Response:**
1. "Naive approach is O(n*m)—I'll use KMP for O(n+m)."
2. "Precompute failure function: for each pattern position, longest prefix-suffix match."
3. "On mismatch, use failure function to skip ahead without re-checking matched characters."

## Related Problems
- [[String Matching Representative Problems]]

## Related Concepts
- [[String Matching]] — broader pattern matching context
- [[KMP]] — specific algorithm


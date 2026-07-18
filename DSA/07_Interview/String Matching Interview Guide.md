---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/string-matching]
canonical: true
related: [[[String Matching]], [[Coding Interviews]]]
---
# String Matching Interview Guide

## Pattern Recognition
**Green Flags:** Find pattern in text; multiple pattern occurrences; efficient substring search beyond naive O(n*m)

## Communication Template
`For single pattern matching, KMP achieves O(n+m) by precomputing failure function that avoids re-examining matched characters after a mismatch.`

## Core Mechanics
```python
def kmp_search(text, pattern):
    def build_failure(pattern):
        failure = [0] * len(pattern)
        j = 0
        for i in range(1, len(pattern)):
            while j > 0 and pattern[i] != pattern[j]:
                j = failure[j-1]
            if pattern[i] == pattern[j]:
                j += 1
            failure[i] = j
        return failure
    
    failure = build_failure(pattern)
    j = 0
    for i in range(len(text)):
        while j > 0 and text[i] != pattern[j]:
            j = failure[j-1]
        if text[i] == pattern[j]:
            j += 1
        if j == len(pattern):
            return i - j + 1  # Match found
    return -1
```

## Common Mistakes
1. Off-by-one in failure function construction
2. Not resetting j correctly on mismatch
3. Using naive O(n*m) when KMP O(n+m) expected for large inputs

## Practice Problems
- Implement strStr(), Repeated Substring Pattern

## Checklist
- [ ] Failure function correctly precomputed
- [ ] O(n+m) complexity confirmed
- [ ] Edge cases handled (empty pattern, pattern longer than text)

## Related
- [[String Matching]]
- [[String Matching Representative Problems]]

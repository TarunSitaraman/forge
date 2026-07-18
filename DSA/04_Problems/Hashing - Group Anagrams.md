---
type: problem
status: stable
tags: [dsa/problem, dsa/hashing]
canonical: true
related: [[[Hashing]], [[Hash Map]]]
difficulty: medium
---
# Hashing - Group Anagrams

## Problem Statement
Given an array of strings, group anagrams together. Return groups in any order.

**Constraints:**
- 1 ≤ strs.length ≤ 10^4
- Strings consist of lowercase English letters
- Anagrams: same characters, different order

## Classification
- **Patterns:** [[Hashing]]
- **Category:** Grouping via canonical key generation

## Pattern Selection
This is canonical for Hashing because:
1. Need O(1) average grouping mechanism
2. Anagrams share a canonical form (sorted characters or character count)
3. Hash map naturally groups by this canonical key

## Why Hashing Works
**Key Insight:** Two strings are anagrams if and only if they have the same canonical representation (sorted string or character frequency signature).

## Python Solution
```python
from collections import defaultdict

def groupAnagrams(strs: list[str]) -> list[list[str]]:
    """
    Group anagrams using sorted string as canonical key.
    Time: O(n * k log k) where k = max string length
    Space: O(n * k) for storing all strings
    """
    groups = defaultdict(list)
    
    for s in strs:
        key = ''.join(sorted(s))  # Canonical form
        groups[key].append(s)
    
    return list(groups.values())


def groupAnagramsCountSignature(strs: list[str]) -> list[list[str]]:
    """
    Alternative: use character count tuple as key (avoids sorting).
    Time: O(n * k) - no sorting needed
    Space: O(n * k)
    """
    groups = defaultdict(list)
    
    for s in strs:
        count = [0] * 26
        for ch in s:
            count[ord(ch) - ord('a')] += 1
        key = tuple(count)  # Tuple is hashable
        groups[key].append(s)
    
    return list(groups.values())
```

## Reasoning
1. **Canonical Key:** Sort each string's characters to get canonical form
2. **Hash Map Grouping:** Use canonical form as dict key, append original string to value list
3. **Return Groups:** Extract all value lists from the hash map

## Complexity Analysis
- **Sorted Key Approach:** O(n·k log k) time (k = avg string length), O(n·k) space
- **Count Signature Approach:** O(n·k) time (no sorting), O(n·k) space

## Edge Cases & Validation
```python
# Basic grouping
result = groupAnagrams(["eat","tea","tan","ate","nat","bat"])
# Groups: [eat,tea,ate], [tan,nat], [bat]

# Empty string
result = groupAnagrams([""])
assert result == [[""]]

# Single string
result = groupAnagrams(["a"])
assert result == [["a"]]

# No anagrams (all unique)
result = groupAnagrams(["abc","def","ghi"])
assert len(result) == 3
```

## Common Mistakes
1. **Using String Itself as Key:** Doesn't group anagrams (they're different strings)
2. **Not Handling Empty Strings:** Should work correctly (empty sorted is still empty)
3. **Wrong Signature for Count Approach:** Must use tuple (hashable), not list (unhashable)

## Interview Walkthrough
**Interviewer:** "Group anagrams together."

**Your Response:**
1. "Anagrams share the same characters, just reordered."
2. "I'll use each string's sorted form as a canonical key for grouping."
3. "Hash map naturally groups strings with matching canonical keys."
4. "Alternative: character count tuple avoids O(k log k) sorting overhead."

## Related Problems
- [[Hashing Representative Problems]]

## Related Concepts
- [[Hashing]] — canonical key technique
- [[Hash Map]] — grouping data structure


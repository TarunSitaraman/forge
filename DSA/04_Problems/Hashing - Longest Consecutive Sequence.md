---
type: problem
status: stable
tags: [dsa/problem, dsa/hashing]
canonical: true
related: [[[Hashing]], [[Hash Set]]]
difficulty: medium
---
# Hashing - Longest Consecutive Sequence

## Problem Statement
Given an unsorted array of integers, find the length of the longest consecutive elements sequence. Must run in O(n) time.

**Constraints:**
- Cannot sort (would be O(n log n), problem requires O(n))
- Numbers may be negative

## Classification
- **Patterns:** [[Hashing]]
- **Category:** Sequence detection via set lookup

## Pattern Selection
This is canonical for Hashing because it demonstrates a clever O(n) technique: only start counting a sequence from its TRUE beginning (a number with no predecessor in the set), avoiding redundant work.

## Why Hashing Works
**Key Insight:** Put all numbers in a hash set for O(1) lookup. For each number, only start counting a sequence if (num-1) is NOT in the set (meaning this num is a sequence start). This ensures each sequence is counted exactly once from its beginning.

## Python Solution
```python
def longestConsecutive(nums: list[int]) -> int:
    """
    Find longest consecutive sequence length using hash set.
    Time: O(n) - each number visited constant number of times
    Space: O(n) for hash set
    """
    if not nums:
        return 0
    
    num_set = set(nums)
    longest = 0
    
    for num in num_set:
        # Only start counting if this is a sequence start
        if num - 1 not in num_set:
            current = num
            length = 1
            
            while current + 1 in num_set:
                current += 1
                length += 1
            
            longest = max(longest, length)
    
    return longest
```

## Reasoning
1. **Build Set:** O(1) lookup for any number
2. **Identify Sequence Starts:** A number is a sequence start if num-1 is NOT in the set
3. **Count Forward:** From each sequence start, count consecutive numbers forward
4. **Why O(n) Overall:** Each number is only "counted" once — either as part of finding its own sequence start check, or once during the forward counting of its sequence (never re-counted)

## Complexity Analysis
- **Time:** O(n) — despite nested while loop, amortized analysis shows each number visited constant times total
- **Space:** O(n) — hash set

## Edge Cases & Validation
```python
assert longestConsecutive([100,4,200,1,3,2]) == 4  # [1,2,3,4]
assert longestConsecutive([]) == 0
assert longestConsecutive([1,2,0,1]) == 3  # [0,1,2], duplicate handled by set
assert longestConsecutive([9,1,4,7,3,-1,0,5,8,-1,6]) == 7  # [-1 to 5? let's verify] actually check the exact sequence
```

## Common Mistakes
1. **Sorting First:** Defeats O(n) requirement (sorting is O(n log n))
2. **Not Checking Sequence Start Condition:** Without `if num-1 not in num_set`, algorithm becomes O(n²) in worst case (re-counting same sequence from every element)
3. **Using List Instead of Set:** O(n) lookup instead of O(1) breaks the complexity guarantee

## Interview Walkthrough
**Interviewer:** "Find longest consecutive sequence, O(n) time required."

**Your Response:**
1. "First thought: sort and scan—but that's O(n log n), too slow."
2. "Instead, I'll use a hash set for O(1) lookups."
3. "Key insight: only start counting from a number that has NO predecessor in the set (true sequence start)."
4. "This ensures each sequence is counted exactly once, giving O(n) overall despite the nested loop appearance."

**Why This Resonates:**
- Shows the non-obvious "sequence start" optimization that achieves true O(n)
- Explains why naive nested loop check would seem O(n²) but isn't due to the start condition

## Related Problems
- [[Hashing - Two Sum]] (different hashing application)
- [[Hashing Representative Problems]]

## Related Concepts
- [[Hashing]] — set-based sequence detection
- [[Hash Set]] — O(1) membership testing


---
type: problem
status: stable
tags: [dsa/problem, dsa/bit-manipulation]
canonical: true
related: [[[Bit Manipulation]]]
difficulty: easy
---
# Bit Manipulation - Single Number

## Problem Statement
Given an array where every element appears twice except one, find that single element. Must run in O(n) time, O(1) space.

**Constraints:**
- Every element appears exactly twice except one
- Must use O(1) extra space (no hash set allowed)

## Classification
- **Patterns:** [[Bit Manipulation]]
- **Category:** XOR-based duplicate cancellation

## Pattern Selection
This is canonical for Bit Manipulation because XOR has the perfect properties for this: a^a=0 (self-cancel) and a^0=a (identity).

## Why XOR Works
**Key Properties:**
- XOR is commutative and associative: order doesn't matter
- a^a = 0: pairs cancel out completely
- a^0 = a: XORing with 0 leaves value unchanged
- Therefore: XORing all elements leaves only the unpaired element

## Python Solution
```python
def singleNumber(nums: list[int]) -> int:
    """
    Find single number using XOR cancellation.
    Time: O(n)
    Space: O(1)
    """
    result = 0
    for num in nums:
        result ^= num
    return result
```

## Reasoning
1. **XOR All Elements:** Process array left to right, XORing running result with each element
2. **Pairs Cancel:** Since XOR is commutative/associative, all paired elements cancel to 0
3. **Single Remains:** Final result is the unpaired element XORed with 0, which equals itself

## Complexity Analysis
- **Time:** O(n) — single pass
- **Space:** O(1) — only one accumulator variable

## Edge Cases & Validation
```python
assert singleNumber([2,2,1]) == 1
assert singleNumber([4,1,2,1,2]) == 4
assert singleNumber([1]) == 1
assert singleNumber([-1,-1,2]) == 2  # Negative numbers work fine
```

## Common Mistakes
1. **Using Hash Set (violates O(1) space constraint):** Works but doesn't meet optimal solution requirements
2. **Not Recognizing XOR Pattern:** Missing the key insight that duplicates cancel

## Interview Walkthrough
**Interviewer:** "Find the single number that doesn't have a pair, O(1) space."

**Your Response:**
1. "XOR has perfect properties here: a^a=0 and a^0=a."
2. "If I XOR all elements together, every pair cancels to 0."
3. "What remains is exactly the single unpaired element."

## Variations
1. **Single Number II:** Every element appears 3 times except one (requires bit counting per position)
2. **Single Number III:** Two elements appear once, rest appear twice (requires partition by distinguishing bit)

## Related Problems
- [[Bit Manipulation Representative Problems]]

## Related Concepts
- [[Bit Manipulation]] — XOR properties


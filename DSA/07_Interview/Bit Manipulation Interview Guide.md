---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/bit-manipulation]
canonical: true
related: [[[Bit Manipulation]], [[Coding Interviews]]]
---
# Bit Manipulation Interview Guide

## Pattern Recognition
**Green Flags:** `single number appears once`, `power of two`, `count set bits`, subset generation via bitmask

## Communication Template
`XOR has the property a^a=0 and a^0=a. If all elements appear twice except one, XOR-ing everything cancels pairs, leaving only the unique element.`

## Core Mechanics
```python
def single_number(nums):
    result = 0
    for num in nums:
        result ^= num
    return result

def is_power_of_two(n):
    return n > 0 and (n & (n - 1)) == 0
```

## Common Mistakes
1. Forgetting two's complement for negative numbers
2. Off-by-one in bit position indexing
3. Confusing XOR with OR

## Practice Problems
- Single Number, Power of Two, Counting Bits, Subsets via Bitmask

## Checklist
- [ ] Verified operation works for negative numbers if applicable
- [ ] Confirmed bit width assumptions (32-bit vs 64-bit)

## Related
- [[Bit Manipulation]]
- [[Bit Manipulation Representative Problems]]

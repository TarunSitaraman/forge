---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/bit-manipulation]
canonical: true
---
# Bit Manipulation Representative Problems

Core problem variants that exercise Bit Manipulation across different bitwise operations and numerical properties.

## Variant 1: Single Number Detection (XOR Properties)
**Recognition:** Find element appearing once when all others appear k times (XOR variant).
**Mechanics:** XOR all elements; ones place gives missing bit; reconstruct number.
**Key Challenge:** Extending beyond k=2 (single XOR) to arbitrary k.

- [[Bit Manipulation - Single Number]]
- Variant: Two numbers appear once
- Variant: All others appear 3 times (single appears once)
- Variant: Find complement (all bits flipped)

## Variant 2: Power of Two & Bit Counting
**Recognition:** Check if number is power of 2, or count set bits.
**Mechanics:** Power of 2 has single bit set: `n & (n-1) == 0`. Counting: iterate bits.
**Key Challenge:** Recognizing power-of-2 pattern; efficient bit counting.

- [[Bit Manipulation - Power of Two]]
- Variant: Hamming weight (count set bits)
- Variant: Number of 1 bits
- Variant: Most significant bit position

## Variant 3: Bit Manipulation for Optimization
**Recognition:** Use bitwise operations to replace arithmetic or loop operations.
**Mechanics:** Bit shifting instead of multiplication/division by powers of 2.
**Key Challenge:** Correctness of bitwise operations; negative number handling.

- Variant: Divide without division operator
- Variant: Multiply two integers
- Variant: Fast exponentiation (binary exponentiation)

## Variant 4: Subset & Combination Generation
**Recognition:** Generate all subsets or combinations using bitmask representation.
**Mechanics:** Iterate from 0 to 2^n-1; each number's bits indicate elements included.
**Key Challenge:** Mapping bits to elements; ordering of subsets.

- Variant: All subsets (power set)
- Variant: Subsets with specific property
- Variant: k-element subsets via bitmask

## Variant 5: Bit-Level State Compression
**Recognition:** Use single integer as compact representation of multiple boolean states.
**Mechanics:** Each bit represents one boolean; bitwise AND/OR/XOR for operations.
**Key Challenge:** Tracking bit meanings; efficient state transitions.

- Variant: Toggle flags
- Variant: Check specific bit set
- Variant: Game state encoding

## Variant 6: Bitwise Operation Properties
**Recognition:** Exploit XOR, AND, OR properties for mathematical insights.
**Mechanics:** XOR: a^a=0, a^0=a, a^b=b^a. AND/OR: commutative, associative.
**Key Challenge:** Recognizing when bitwise operations reveal hidden properties.

- Variant: Two sum using bitmask (small range)
- Variant: Subset XOR target
- Variant: Maximum XOR subarray

## Cross-Linking
- **Pattern:** [[Bit Manipulation]]
- **Related Patterns:** [[Number Theory]], [[Dynamic Programming]]
- **Math Concepts:** [[Powers of Two]], [[Binary Representation]]
- **Interview Guide:** [[Bit Manipulation Interview Guide]]

## Key Bitwise Operations
| Operation | Symbol | Example | Result |
|---|---|---|---|
| **AND** | & | 5 & 3 | 0101 & 0011 = 0001 (1) |
| **OR** | \| | 5 \| 3 | 0101 \| 0011 = 0111 (7) |
| **XOR** | ^ | 5 ^ 3 | 0101 ^ 0011 = 0110 (6) |
| **NOT** | ~ | ~5 | ~0101 = 1010 (-6 in two's complement) |
| **LEFT** | << | 5 << 1 | 0101 << 1 = 1010 (10) |
| **RIGHT** | >> | 5 >> 1 | 0101 >> 1 = 0010 (2) |

## Common Pitfalls
- Confusing XOR with OR (both look similar)
- Forgetting about two's complement for negative numbers
- Off-by-one in bit positions (0-indexed)
- Not handling bit width correctly (32-bit vs. 64-bit)
- Assuming bitwise operations work like arithmetic

## Interview Red Flags
- "Appears k times" → XOR or modular arithmetic
- "Power of 2" → Check `n & (n-1) == 0`
- "Set/unset bit" → Use `&`, `|` with masks
- "Generate all subsets" → Bitmask iteration 0 to 2^n-1
- "Optimize with bits" → Consider bit shifting


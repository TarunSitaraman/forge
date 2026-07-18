---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/bit-manipulation]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Bit Manipulation Cheat Sheet

## Signals
Single number (XOR); power of two; count set bits; subset generation via bitmask.

## Core Moves
- XOR: a^a=0, a^0=a (single number detection)
- Power of 2: n & (n-1) == 0
- Count bits: n & (n-1) removes lowest set bit
- Subsets: iterate 0 to 2^n-1

## Complexity
O(1) per operation typically; O(2^n) for subset generation.

## Template Links
- [[Python - Bit Manipulation]]

## Watch For
- Two's complement for negative numbers
- Off-by-one in bit positions

## Canonical Depth
See [[Bit Manipulation]] for full explanation.

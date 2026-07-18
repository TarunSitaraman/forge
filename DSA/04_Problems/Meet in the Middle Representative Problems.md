---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/meet-in-middle]
canonical: true
---
# Meet in the Middle Representative Problems

Core variants exercising Meet in the Middle technique for reduced complexity.

## Variant 1: 4Sum Type Problems
Partition into two halves; generate all sums; use hash map to find matches.
- Reduce O(n^3) to O(n^2)
- Two subset sum pairing

## Variant 2: Subset Sum Variants  
- Two disjoint subsets with equal sum
- Closest pair sum to target
- K-sum with meet in middle

## Variant 3: State Space Search
- Reduce from O(2^n) to O(2^(n/2))
- Useful when n ≤ 40 (2^20 ≈ 1M)

## Variant 4: Balanced Partition
- Partition set into balanced subsets
- Minimize difference
- Target subset sum

## Cross-Linking
- **Pattern:** [[Meet in the Middle]]
- **Related:** [[Hash Map]], [[Divide and Conquer]]
- **Interview Guide:** [[Meet in the Middle Interview Guide]]

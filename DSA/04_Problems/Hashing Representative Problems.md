---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/hashing]
canonical: true
---
# Hashing Representative Problems

Core problem variants that exercise Hashing for O(1) lookups and deduplication.

## Variant 1: Duplicate Detection & Deduplication
**Recognition:** Check for duplicates or remove them efficiently.
**Mechanics:** Store in set/hash map; O(1) lookup; track seen elements.
**Key Challenge:** Handling multiple duplicates; preserving order.

- [[Hashing - Contains Duplicate]]
- Variant: First unique character
- Variant: Find duplicate

## Variant 2: Two Sum & Multi-Sum
**Recognition:** Find pairs/triplets summing to target.
**Mechanics:** Hash map stores complement; O(n) single pass.
**Key Challenge:** Avoiding counting same element twice.

- [[Hashing - Two Sum]]
- Variant: 3Sum, 4Sum
- Variant: Sum of unique pairs

## Variant 3: Frequency Tracking
**Recognition:** Count occurrences and find patterns (most frequent, k frequent).
**Mechanics:** Hash map from value to frequency.
**Key Challenge:** Tie-breaking in frequency-based problems.

- Variant: Top k frequent elements
- Variant: Majority element
- Variant: Uncommon from sentences

## Variant 4: Substring Patterns
**Recognition:** Find substrings matching patterns or properties.
**Mechanics:** Hash map for character/substring frequency.
**Key Challenge:** Distinguishing "all unique" vs. frequency counts.

- Variant: Isomorphic strings (character mapping)
- Variant: Ransom note (character availability)
- Variant: Word pattern (bijection)

## Variant 5: Collision Handling
**Recognition:** Design hash table with good collision resolution.
**Mechanics:** Chaining (linked list) or open addressing (probing).
**Key Challenge:** Load factor; cache efficiency.

## Variant 6: Custom Hash Functions
**Recognition:** Hash complex objects or maintain invariants.
**Mechanics:** Override hash method for custom equality.
**Key Challenge:** Hash consistency; collision probability.

## Cross-Linking
- **Pattern:** [[Hashing]]
- **Data Structure:** [[Hash Map]], [[Hash Set]]
- **Interview Guide:** [[Hashing Interview Guide]]

## Common Pitfalls
- Hash collisions unhandled
- Not counting duplicates correctly
- Mutable objects as keys (can change hash)
- Memory inefficiency with large hash maps


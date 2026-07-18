---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/fast-slow-pointers]
canonical: true
---
# Fast & Slow Pointers Representative Problems

Core problem variants that exercise Fast and Slow Pointer pattern on linked lists and cycles.

## Variant 1: Cycle Detection
**Recognition:** Detect if linked list contains cycle.
**Mechanics:** Slow moves 1 step, fast moves 2 steps. If meet → cycle exists.
**Key Challenge:** Handling no-cycle case (fast reaches null).

- [[Fast & Slow Pointers - Cycle Detection]]
- Variant: Find cycle start node
- Variant: Cycle length determination

## Variant 2: Find Middle Element
**Recognition:** Find middle node of linked list in single pass.
**Mechanics:** Slow moves 1, fast moves 2; fast reaches end → slow at middle.
**Key Challenge:** Even/odd length lists; where "middle" is for even-length.

- [[Fast & Slow Pointers - Middle of Linked List]]
- Variant: Delete middle element
- Variant: Find kth from end

## Variant 3: Palindrome Checking
**Recognition:** Check if linked list is palindrome.
**Mechanics:** Find middle, reverse second half, compare with first half.
**Key Challenge:** Reversing in-place; restoring original structure.

- [[Fast & Slow Pointers - Palindrome Linked List]]
- Variant: Palindrome array variant (simpler)
- Variant: Skip palindrome subsequences

## Variant 4: Linked List Rearrangement
**Recognition:** Rearrange list (merge, reorder, alternating).
**Mechanics:** Use fast/slow to find split point; manipulate independently; merge.
**Key Challenge:** Maintaining pointers during rearrangement; avoiding cycles.

- Variant: Reorder list (first, last, first, last...)
- Variant: Merge sorted lists
- Variant: Interleave lists

## Variant 5: Kth Node Operations
**Recognition:** Find or manipulate kth node from start or end.
**Mechanics:** Two-pointer gap technique: maintain k-step distance between pointers.
**Key Challenge:** Handling k larger than list length; pointer initialization.

- [[Fast & Slow Pointers - Kth from End]]
- Variant: Remove kth node
- Variant: Rotate list k positions

## Cross-Linking
- **Pattern:** [[Fast & Slow Pointers]]
- **Related Patterns:** [[Two Pointers]], [[Linked List]]
- **Data Structure:** [[Linked List]]
- **Interview Guide:** [[Fast & Slow Pointers Interview Guide]]

## Pattern Variants
| Problem | Slow Step | Fast Step | When Meet |
|---|---|---|---|
| **Cycle Detection** | +1 | +2 | Cycle exists |
| **Middle Find** | +1 | +2 | Slow at middle |
| **Kth from End** | +1 | +1 (k-step gap) | Pointers aligned |

## Common Pitfalls
- Fast pointer null check (must verify at each step for +2)
- Slow/fast initialization (off-by-one in positioning)
- Forgetting to handle single-node or empty list
- Cycle start calculation (distance = head to cycle start)

## Interview Red Flags
- "Cycle in linked list" → Fast/slow pointers
- "Middle of linked list" → Fast/slow pointers
- "Palindrome check" → Fast/slow for middle + reverse
- "Kth from end" → Two-pointer gap technique


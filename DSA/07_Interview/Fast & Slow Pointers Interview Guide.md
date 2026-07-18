---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/fast-slow-pointers]
canonical: true
related: [[[Fast & Slow Pointers]], [[Coding Interviews]]]
---
# Fast & Slow Pointers Interview Guide

## Pattern Recognition
**Green Flags:** Linked list cycle detection; finding middle element; palindrome checking on linked list

## Communication Template
`Using two pointers moving at different speeds (1x and 2x), if there's a cycle they must eventually meet. If no cycle, fast pointer reaches null first.`

## Core Mechanics
```python
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True
    return False
```

## Common Mistakes
1. Not checking fast.next before fast.next.next (null pointer error)
2. Off-by-one in middle-finding for even-length lists
3. Forgetting to handle single-node or empty list

## Practice Problems
- Linked List Cycle, Middle of Linked List, Palindrome Linked List

## Checklist
- [ ] Null checks before advancing fast pointer
- [ ] Handled even/odd length edge cases
- [ ] Verified single-node and empty list cases

## Related
- [[Fast & Slow Pointers]]
- [[Fast & Slow Pointers Representative Problems]]

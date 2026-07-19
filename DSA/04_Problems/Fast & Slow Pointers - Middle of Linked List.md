---
type: problem
status: stable
tags: [dsa/problem, dsa/fast-slow-pointers, dsa/linked-list]
canonical: true
related: [[[Fast & Slow Pointers]], [[Linked List]]]
difficulty: easy
---
# Fast & Slow Pointers - Middle of Linked List

## Problem Statement
Given the head of a linked list, return the middle node. If there are two middle nodes, return the second one.

**Constraints:**
- Single pass required (no counting nodes first)
- Handle both even and odd length lists

## Classification
- **Patterns:** [[Fast & Slow Pointers]]
- **Category:** Single-pass middle detection

## Pattern Selection
This is the simplest canonical introduction to Fast & Slow Pointers: fast moves twice as fast as slow, so when fast reaches the end, slow is exactly at the middle.

## Why This Approach Works
**Key Insight:** If fast pointer moves 2 steps for every 1 step of slow, when fast has traversed the entire list, slow has traversed exactly half—landing on the middle.

## Python Solution
```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

def middleNode(head: ListNode) -> ListNode:
    """
    Find middle node using fast/slow pointers.
    Time: O(n) single pass
    Space: O(1)
    """
    slow = fast = head
    
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    return slow
```

## Reasoning
1. **Initialize Both at Head:** slow = fast = head
2. **Move at Different Speeds:** slow +1, fast +2 each iteration
3. **Termination:** When fast reaches end (or fast.next is null), slow is at middle
4. **Even Length Handling:** For even-length lists, this naturally lands on the SECOND middle node (as required)

## Complexity Analysis
- **Time:** O(n) — single pass
- **Space:** O(1) — only two pointer variables

## Edge Cases & Validation
```python
# Odd length: 1->2->3->4->5, middle is 3
head = ListNode(1, ListNode(2, ListNode(3, ListNode(4, ListNode(5)))))
assert middleNode(head).val == 3

# Even length: 1->2->3->4, middle is 3 (second middle, per problem spec)
head = ListNode(1, ListNode(2, ListNode(3, ListNode(4))))
assert middleNode(head).val == 3

# Single node
head = ListNode(1)
assert middleNode(head).val == 1

# Two nodes
head = ListNode(1, ListNode(2))
assert middleNode(head).val == 2
```

## Common Mistakes
1. **Wrong Loop Condition:** Using `while fast.next and fast.next.next` shifts the "middle" definition (changes even-length behavior)
2. **Not Checking fast.next Before fast.next.next:** Null pointer error when fast lands on last node
3. **Two-Pass Solution:** Counting length first then traversing again works but misses the elegant single-pass technique

## Interview Walkthrough
**Interviewer:** "Find middle of linked list in one pass."

**Your Response:**
1. "I'll use two pointers: slow moves 1 step, fast moves 2 steps per iteration."
2. "When fast reaches the end, slow has traveled exactly half the distance—landing on the middle."
3. "For even-length lists, this naturally gives the second middle node, matching the problem requirement."

## Variations
1. **Return First Middle for Even Length:** Adjust loop condition slightly
2. **Delete Middle Node:** Extension requiring tracking the node before middle
3. **Check Palindrome:** Uses middle-finding as first step, then reverses second half

## Related Problems
- [[Fast & Slow Pointers - Cycle Detection]] (same technique, different application)
- [[Fast & Slow Pointers Representative Problems]]

## Related Concepts
- [[Fast & Slow Pointers]] — core dual-speed technique
- [[Linked List]] — data structure context


---
type: problem
status: stable
tags: [dsa/problem, dsa/fast-slow-pointers, dsa/linked-list]
canonical: true
related: [[[Fast & Slow Pointers]], [[Linked List]]]
difficulty: medium
---
# Fast & Slow Pointers - Cycle Detection (Floyd's Algorithm)

## Problem Statement
Given a linked list, determine if it contains a cycle. If so, return the node where the cycle begins.

**Constraints:**
- List may or may not contain a cycle
- Must use O(1) space (no hash set allowed for optimal solution)

## Classification
- **Patterns:** [[Fast & Slow Pointers]]
- **Category:** Cycle detection using Floyd's Tortoise and Hare

## Pattern Selection
This is canonical for Fast & Slow Pointers because:
1. Classic Floyd's cycle detection algorithm
2. Two pointers at different speeds MUST meet if cycle exists
3. Mathematical proof: if cycle exists, relative speed guarantees eventual meeting

## Why This Approach Works
**Mathematical Proof:**
- If cycle exists, once both pointers enter cycle, fast gains 1 step on slow each iteration
- Eventually the gap becomes 0 (they meet) since gap decreases by 1 each step within a cycle of finite length

**Finding Cycle Start (Phase 2):**
- After detecting meeting point, reset one pointer to head
- Move both pointers at same speed (1 step each)
- They meet exactly at cycle start (provable via math: distance from head to cycle start equals distance from meeting point to cycle start, going around)

## Python Solution
```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

def detectCycle(head: ListNode) -> ListNode:
    """
    Detect cycle and find its starting node using Floyd's algorithm.
    Time: O(n)
    Space: O(1)
    """
    if not head or not head.next:
        return None
    
    slow = fast = head
    
    # Phase 1: Detect if cycle exists
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            break
    else:
        return None  # No cycle (fast reached end)
    
    # Phase 2: Find cycle start
    slow = head
    while slow != fast:
        slow = slow.next
        fast = fast.next
    
    return slow  # This is the cycle start
```

## Reasoning
1. **Phase 1 - Detection:** Slow moves 1 step, fast moves 2 steps; if they meet, cycle exists
2. **Termination Check:** If fast reaches null (or fast.next is null), no cycle exists
3. **Phase 2 - Find Start:** Reset slow to head; move both at same speed until they meet again
4. **Mathematical Guarantee:** Second meeting point IS the cycle start (provable via distance equations)

## Complexity Analysis
- **Time:** O(n) — each phase is linear
- **Space:** O(1) — only two pointer variables

## Edge Cases & Validation
```python
# No cycle
head = ListNode(1, ListNode(2, ListNode(3)))
assert detectCycle(head) is None

# Single node, no cycle
head = ListNode(1)
assert detectCycle(head) is None

# Single node, self-cycle
head = ListNode(1)
head.next = head
assert detectCycle(head) == head

# Cycle starts at head
a = ListNode(1)
b = ListNode(2)
a.next = b
b.next = a
assert detectCycle(a) == a

# Cycle starts in middle
a = ListNode(1)
b = ListNode(2)
c = ListNode(3)
a.next = b
b.next = c
c.next = b  # Cycle back to b
assert detectCycle(a) == b
```

## Common Mistakes
1. **Not Checking fast.next Before fast.next.next:** Causes null pointer error
2. **Confusing Detection with Finding Start:** These are two distinct phases requiring different logic
3. **Not Resetting Slow to Head:** Phase 2 requires slow to restart from head, not continue from meeting point

## Interview Walkthrough
**Interviewer:** "Detect cycle in linked list and find where it starts."

**Your Response:**
1. "I'll use Floyd's algorithm: slow pointer moves 1 step, fast moves 2 steps."
2. "If there's a cycle, they must eventually meet (fast gains 1 step per iteration in the cycle)."
3. "To find cycle start, I reset slow to head, then move both at same speed—they meet exactly at cycle start."
4. "This is provable mathematically using distance relationships."

## Related Problems
- [[Fast & Slow Pointers Representative Problems]]

## Related Concepts
- [[Fast & Slow Pointers]] — core technique
- [[Linked List]] — data structure context


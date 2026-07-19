---
type: problem
status: stable
tags: [dsa/problem, dsa/recursion]
canonical: true
related: [[[Recursion]]]
difficulty: medium
---
# Recursion - Tower of Hanoi

## Problem Statement
Given n disks on rod A (stacked largest-to-smallest), move all disks to rod C using rod B as auxiliary. Rules: only one disk moved at a time, only smaller disk can go on larger disk, only top disk of any rod can be moved.

**Constraints:**
- Classic recursive puzzle
- Minimum moves required: 2^n - 1

## Classification
- **Patterns:** [[Recursion]]
- **Category:** Classic recursive problem demonstrating recursive decomposition thinking

## Pattern Selection
This is the canonical teaching example for Recursion because the recursive insight is elegant but not obvious: to move n disks from A to C, first move n-1 disks from A to B (using C as auxiliary), move the largest disk A to C, then move n-1 disks from B to C (using A as auxiliary).

## Why This Approach Works
**Key Insight (Recursive Decomposition):**
- To move n disks from source to destination using auxiliary:
  1. Move top n-1 disks from source to auxiliary (using destination as helper)
  2. Move the nth (largest) disk from source to destination directly
  3. Move n-1 disks from auxiliary to destination (using source as helper)

## Python Solution
```python
def hanoi(n: int, source: str, destination: str, auxiliary: str, moves: list = None) -> list:
    """
    Solve Tower of Hanoi, returning list of moves.
    Time: O(2^n) - matches minimum required moves
    Space: O(n) recursion depth
    """
    if moves is None:
        moves = []
    
    if n == 1:
        moves.append((source, destination))
        return moves
    
    # Move n-1 disks from source to auxiliary
    hanoi(n - 1, source, auxiliary, destination, moves)
    
    # Move the largest disk from source to destination
    moves.append((source, destination))
    
    # Move n-1 disks from auxiliary to destination
    hanoi(n - 1, auxiliary, destination, source, moves)
    
    return moves


def hanoiMoveCount(n: int) -> int:
    """Minimum number of moves required."""
    return (1 << n) - 1  # 2^n - 1
```

## Reasoning
1. **Base Case:** Single disk (n=1) — move directly from source to destination
2. **Recursive Case:** 
   - Move n-1 disks out of the way (to auxiliary)
   - Move the largest disk (now uncovered) to destination
   - Move the n-1 disks from auxiliary onto destination
3. **Recursive Trust:** Trust that hanoi(n-1, ...) correctly solves the smaller subproblem

## Complexity Analysis
- **Time:** O(2^n) — T(n) = 2T(n-1) + 1, solving gives 2^n - 1
- **Space:** O(n) — recursion depth equals number of disks

## Edge Cases & Validation
```python
assert hanoiMoveCount(1) == 1
assert hanoiMoveCount(3) == 7
assert hanoiMoveCount(10) == 1023

moves = hanoi(2, 'A', 'C', 'B')
assert len(moves) == 3
assert moves == [('A','B'), ('A','C'), ('B','C')]
```

## Common Mistakes
1. **Wrong Auxiliary/Destination Swap:** Confusing which rod is auxiliary in recursive calls
2. **Not Trusting Recursion:** Trying to trace through all disk movements manually instead of trusting the recursive contract
3. **Missing Base Case:** Infinite recursion without n=1 termination

## Interview Walkthrough
**Interviewer:** "Solve Tower of Hanoi recursively."

**Your Response:**
1. "The key insight: to move n disks, I need to get n-1 disks 'out of the way' first."
2. "Move top n-1 disks to auxiliary rod, move the largest disk directly, then move n-1 disks from auxiliary to destination."
3. "I trust the recursive call correctly handles the n-1 subproblem—that's the recursive leap of faith."
4. "This gives O(2^n) time, matching the mathematically proven minimum move count."

**Why This Resonates:**
- Demonstrates the "recursive leap of faith" mental model explicitly
- Shows understanding of why swapping source/auxiliary/destination in recursive calls works

## Related Problems
- [[Recursion - Merge Sort]] (different recursive decomposition style)
- [[Recursion Representative Problems]]

## Related Concepts
- [[Recursion]] — recursive decomposition and trust
- [[Divide and Conquer]] — related but different structure (Hanoi isn't truly divide-conquer since subproblems aren't independent)


---
type: problem
status: stable
tags: [dsa/problem, dsa/two-pointers, dsa/in-place]
canonical: true
related: [[[Two Pointers]], [[Python - Two Pointers]]]
difficulty: easy
---
# Two Pointers - Remove Duplicates

## Problem Statement
Given a **sorted** array containing duplicates, remove duplicates **in-place** such that each unique element appears exactly once. Return the length of the modified array.

**Constraints:**
- Array is sorted
- Modify in-place (O(1) extra space)
- Order of elements must be preserved
- Elements after the new length don't matter (but shouldn't be garbage)

## Classification
- **Patterns:** [[Two Pointers]]
- **Template:** [[Python - Two Pointers]]
- **Category:** In-place array modification with write/read pointers

## Pattern Selection
This is canonical for Two Pointers in-place modification because:
1. Write pointer (left) tracks the position for next unique element
2. Read pointer (right) scans forward to find candidates
3. Two pointers maintain disjoint invariant: everything left of write pointer is finalized
4. No extra data structures needed

This pattern generalizes to: remove element X, move zeros to end, sort colors (partition), etc.

## Why Two Pointers Works
**Invariant:** 
- Left pointer (write): marks position of next element to place
- Right pointer (read): scans to find next unique element
- All elements before left pointer are finalized and unique

**Movement:**
- When a new unique element is found at right pointer:
  - Copy to left pointer position
  - Increment left pointer
  - This maintains the invariant that elements [0, left) are unique

Because array is sorted, duplicates are consecutive, making detection trivial.

## Python Solution
```python
def remove_duplicates(nums: list[int]) -> int:
    """
    Remove duplicates in-place from sorted array.
    Time: O(n) single pass
    Space: O(1) no extra data structures
    
    Example:
        Input: [1, 1, 2]
        Output: 2 (array becomes [1, 2, _])
    """
    if not nums:
        return 0
    
    # left: position to place next unique element
    left = 0
    
    # right: scan for next element to consider
    for right in range(1, len(nums)):
        # Found a new unique element (different from previous)
        if nums[right] != nums[left]:
            left += 1
            nums[left] = nums[right]
    
    # Length is left + 1 (0-indexed to count)
    return left + 1
```

## Reasoning
1. **Initialize:** left = 0 (first element is always unique)
2. **Scan:** right pointer moves through entire array
3. **Comparison:** Is nums[right] different from nums[left]?
   - **Yes:** Found new unique element
     - Move write pointer left++
     - Copy value nums[left] = nums[right]
   - **No:** Duplicate found, skip it (don't increment left)
4. **Return:** left + 1 is the count of unique elements

## Complexity Analysis
- **Time:** O(n) — single pass with right pointer
- **Space:** O(1) — only two integer pointers, no extra collections

## Edge Cases & Validation
```python
# No duplicates
assert remove_duplicates([1, 2, 3]) == 3

# All duplicates
assert remove_duplicates([1, 1, 1]) == 1

# Empty array
assert remove_duplicates([]) == 0

# Single element
assert remove_duplicates([1]) == 1

# Duplicates at start
assert remove_duplicates([1, 1, 2, 3]) == 3

# Duplicates at end
assert remove_duplicates([1, 2, 2, 2]) == 2

# Mixed duplicates
assert remove_duplicates([1, 1, 1, 2, 2, 3]) == 3
```

## Common Mistakes
1. **[[Boundary Errors]]**: Starting right at 0 instead of 1 (compares element to itself)
2. **[[Off-by-One]]**: Returning `left` instead of `left + 1` (off by one)
3. **Overwriting Too Early:** Incrementing left before copying value
4. **Modifying Input Unnecessarily:** Treating this like a "remove" operation instead of "modify in-place"

## Interview Walkthrough
**Interviewer:** "Remove duplicates in-place from sorted array."

**Your Response:**
1. "Since the array is sorted, duplicates are consecutive."
2. "I'll use two pointers: one tracks where to write, one scans ahead."
3. "When I find a value different from the current unique element, I copy it forward."
4. "This avoids extra space and modifies the array in a single pass."

**Why This Resonates:**
- Shows understanding of in-place modification pattern
- Explains write/read pointer distinction clearly
- Demonstrates sorting enables efficient duplicate detection

## Why This Matters
In-place algorithms are critical in:
- Embedded systems (limited memory)
- Stream processing (can't store entire input)
- Interview contexts (testing algorithm design, not language features)

## Follow-Up Variations
1. **Remove Element X:** Instead of "all unique", remove specific value
   - Move all non-target elements left, return new length
   - Same two-pointer pattern

2. **Move Zeros to End:** Partition zeros to end, maintain order
   - Write pointer = last non-zero position
   - Same invariant preservation

3. **Sort Colors:** Partition array into 0s, 1s, 2s in-place
   - Extend to three pointers (left, mid, right)
   - Similar region invariant

4. **Remove Duplicates II:** Keep at most 2 occurrences
   - Track count of current element
   - Increment left only if count < 2

## Interview Prep Notes
- **Common Ask:** "What's the in-place constraint's purpose?"
- **Variant Pressure:** "Remove element X" or "keep duplicates up to k times"
- **Follow-Up:** "Can you do this with linked list?" (similar concept, different implementation)

## Related Problems
- [[Two Pointers - Two Sum Sorted]] (convergent pointers, exact target)
- [[Two Pointers - Container With Most Water]] (convergent pointers, optimization)
- [[Two Pointers Representative Problems]]

## Related Concepts
- [[Two Pointers]] — write/read pointer coordination
- [[In-Place Algorithms]] — space-efficient modifications
- [[Array]] — contiguous memory operations
- [[Sorting]] — why sorted input simplifies logic


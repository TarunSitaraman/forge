---
type: problem
status: stable
tags: [dsa/problem, dsa/two-pointers-container-with-most-water]
canonical: true
related: [[[Two Pointers]], [[Greedy]], [[Python - Two Pointers]]]
---
# Two Pointers - Container With Most Water

## Problem Statement
Given an array of integers representing heights, find two lines that together with the x-axis form a container that holds the maximum amount of water.

**Constraints:**
- You cannot tilt the container
- Water is bounded by the shorter line
- Area = width × min(height1, height2)

## Classification
- **Patterns:** [[Two Pointers]], [[Greedy]]
- **Template:** [[Python - Two Pointers]]
- **Category:** Optimize quantity between two positions

## Pattern Selection
This problem is canonical for the Two Pointers pattern because:
1. Spatial structure (left/right positioning, not value-sorted)
2. Greedy boundary choice is valid: moving the taller line can never increase area
3. Moving the shorter line might increase area (has potential to improve height)

The key insight moves this beyond brute-force O(n²) enumeration.

## Why Two Pointers Works
**Invariant:** The optimal answer involving the current pair of lines is captured in the area calculation.

**Greedy Choice:** Always move the pointer at the shorter line. Why?
- Moving taller line: width decreases, height capped by shorter line → area strictly decreases
- Moving shorter line: width decreases, but height might increase → might find better area

This guarantees we never skip the optimal answer through careful boundary selection.

## Python Solution
```python
def max_area(height: list[int]) -> int:
    """
    Find maximum water area using two pointers.
    Time: O(n) single pass
    Space: O(1) only tracking pointers and max
    """
    left, right = 0, len(height) - 1
    best = 0
    
    while left < right:
        # Calculate area bounded by current pair
        width = right - left
        current_area = width * min(height[left], height[right])
        best = max(best, current_area)
        
        # Move the pointer at shorter height (greedy choice)
        if height[left] < height[right]:
            left += 1
        else:
            right -= 1
    
    return best
```

## Reasoning
1. **Start:** Pointers at extremes (maximum possible width)
2. **Invariant:** Track the best area seen so far
3. **Movement:** Always move the shorter line's pointer inward
   - Guarantees we explore all meaningful configurations
   - Avoids examining dominated solutions
4. **Termination:** Pointers meet (all widths examined)

## Complexity Analysis
- **Time:** O(n) — each pointer moves at most n times, single pass
- **Space:** O(1) — only variables for pointers and result tracking

## Edge Cases & Validation
```python
# Single element: area is always 0
assert max_area([1]) == 0

# Two elements: area is min(h1, h2)
assert max_area([1, 1]) == 1
assert max_area([2, 3]) == 2

# Classic example
assert max_area([1, 8, 6, 2, 5, 4, 8, 3, 7]) == 49  # (index 1, index 8): width=7, height=7

# Greedy choice validation
assert max_area([2, 3, 4, 5, 18, 17, 6]) == 17  # (indices 4,5): width=1, height=17
```

## Common Mistakes
1. **[[Boundary Errors]]**: Using `left <= right` instead of `left < right` (double-counts meeting point)
2. **[[Wrong Greedy Move]]**: Moving the taller line instead of shorter (misses optimal solutions)
3. **Moving Both Pointers:** Only move one at each step; moving both skips valid states

## Interview Walkthrough
**Interviewer:** "Maximize water area. What structure can you exploit?"

**Your Response:**
1. "Area depends on width and the minimum height. Starting at maximum width gives best baseline."
2. "I use two pointers from the ends and move inward, tracking the maximum area."
3. "The key insight: moving the taller line guarantees area won't improve (width shrinks, height bounded by shorter line)."
4. "Moving the shorter line has potential to increase height despite losing width."

**Why This Resonates:**
- Demonstrates understanding of invariants, not just code pattern-matching
- Explains why greedy boundary choice is mathematically sound
- Shows you reason about optimality, not just implementation

## Variations
1. **Trapping Rain Water:** Different geometry (2D height profiles); requires dynamic programming or separate pointer passes
2. **With Constraints:** Each barrier has max capacity; optimization target changes
3. **Maximize Perimeter:** Different measurement entirely (no longer area-based)

## Interview Prep Notes
- **Common Follow-up:** "Can you prove no element is skipped?"
- **Variant Pressure:** "What if we need to track which lines we used?"
- **Generalization:** "How does this extend to 3D or n-dimensional containers?"

## Related Problems
- [[Two Pointers - Two Sum Sorted]] (convergent pointers, exact target)
- [[Two Pointers - Remove Duplicates]] (in-place modification variant)
- [[Two Pointers Representative Problems]]

## Related Concepts
- [[Greedy]] — why we can safely move the shorter line
- [[Two Pointers]] — pointer coordination and invariant preservation
- [[Time Complexity]] — O(n) vs. brute-force O(n²)


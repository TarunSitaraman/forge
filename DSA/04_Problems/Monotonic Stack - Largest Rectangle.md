---
type: problem
status: stable
tags: [dsa/problem, dsa/monotonic-stack]
canonical: true
related: [[[Monotonic Stack]], [[Stack]]]
difficulty: hard
---
# Monotonic Stack - Largest Rectangle in Histogram

## Problem Statement
Given an array representing histogram bar heights, find the area of the largest rectangle that can be formed within the histogram.

**Constraints:**
- Bars have width 1
- Heights are non-negative integers
- Rectangle must be formed by consecutive bars

## Classification
- **Patterns:** [[Monotonic Stack]]
- **Category:** Span-based area maximization

## Pattern Selection
This is canonical (and classic hard-level) for Monotonic Stack because:
1. For each bar, need to find how far left/right it can extend at its height
2. Monotonic stack efficiently finds "next smaller element" in both directions
3. When popping from stack, we can calculate area using the popped bar's height and current span

## Why Monotonic Stack Works
**Key Insight:** When we pop a bar from the stack (because current bar is shorter), the popped bar's rectangle width is determined by: current index - index of new stack top - 1.

**Invariant:** Stack maintains indices of bars in increasing height order. When we encounter a shorter bar, we know the popped bar's maximum extent (since it can't extend past the current position).

## Python Solution
```python
def largestRectangleArea(heights: list[int]) -> int:
    """
    Find largest rectangle area in histogram using monotonic stack.
    Time: O(n) - each bar pushed/popped once
    Space: O(n) for stack
    """
    stack = []  # Stores indices, maintains increasing height order
    max_area = 0
    heights = heights + [0]  # Sentinel to flush remaining stack
    
    for i, h in enumerate(heights):
        while stack and heights[stack[-1]] > h:
            height = heights[stack.pop()]
            # Width: if stack empty, extends to start; else from new top+1
            width = i if not stack else i - stack[-1] - 1
            max_area = max(max_area, height * width)
        stack.append(i)
    
    return max_area
```

## Reasoning
1. **Monotonic Stack Setup:** Maintain indices with increasing heights
2. **Sentinel Addition:** Append 0 height at end to force flush of remaining stack
3. **Pop Condition:** When current height < stack top's height, pop and calculate area
4. **Width Calculation:** 
   - If stack empty after pop: width = current index (extends from start)
   - Otherwise: width = current index - new stack top index - 1
5. **Track Maximum:** Update max_area with each calculated rectangle

## Complexity Analysis
- **Time:** O(n) — each index pushed once, popped at most once
- **Space:** O(n) — stack can hold all indices in worst case (strictly increasing heights)

## Edge Cases & Validation
```python
# Simple case
assert largestRectangleArea([2,1,5,6,2,3]) == 10  # Rectangle: height 5, width 2 -> wait, actually [5,6] gives 5*2=10

# All same height
assert largestRectangleArea([3,3,3,3]) == 12  # 3*4

# Strictly increasing
assert largestRectangleArea([1,2,3,4,5]) == 9  # height 3, width 3

# Strictly decreasing
assert largestRectangleArea([5,4,3,2,1]) == 9  # height 3, width 3

# Single bar
assert largestRectangleArea([5]) == 5

# Empty (edge case)
assert largestRectangleArea([]) == 0
```

## Common Mistakes
1. **Wrong Width Calculation:** Forgetting the -1 when stack isn't empty (off-by-one)
2. **Missing Sentinel:** Not adding 0 at end, missing rectangles using bars up to the last index
3. **Storing Values Instead of Indices:** Need indices to calculate width correctly
4. **[[Boundary Errors]]:** Not handling empty stack case in width calculation

## Interview Walkthrough
**Interviewer:** "Find largest rectangle in histogram."

**Your Response:**
1. "I'll use a monotonic increasing stack of indices."
2. "When current bar is shorter than stack top, I pop and calculate the popped bar's max rectangle."
3. "Width is determined by the gap between current position and the new stack top (after popping)."
4. "I add a sentinel 0 at the end to ensure all bars get processed."

**Why This Resonates:**
- Shows understanding of the non-obvious width calculation
- Explains why monotonic stack captures "how far can this bar extend"
- Demonstrates the sentinel technique for clean termination

## Variations
1. **Maximal Rectangle in Binary Matrix:** Extends this problem to 2D using histogram per row
2. **Trapping Rain Water:** Related but different (finds water trapped, not max rectangle)

## Related Problems
- [[Monotonic Stack Representative Problems]]

## Related Concepts
- [[Monotonic Stack]] — core technique
- [[Stack]] — underlying data structure


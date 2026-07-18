---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/prefix-sum]
canonical: true
related: [[[Prefix Sum]], [[Coding Interviews]], [[Communicating Thought Process]]]
---
# Prefix Sum Interview Guide

This guide prepares you to communicate and defend Prefix Sum solutions in technical interviews.

## Pattern Recognition (30 seconds)
When you see a problem, ask yourself:

**Green Flags for Prefix Sum:**
- "Subarray" + "sum" constraint
- "Sum of [i, j]" needs fast queries
- Array is immutable (won't change during queries)
- Repeated range operations
- "At most k" or "exactly k" or "at least k" sum

**Yellow Flags (may be Prefix Sum, may not):**
- Sliding window with sum constraint — might be simpler with window
- "Cumulative" language — could be difference array instead
- Range updates — difference array is better

**Red Flags (probably NOT Prefix Sum):**
- "Online queries" with updates — use segment tree
- "2D matrix updates" — use 2D segment tree or Fenwick
- Single query only — doesn't justify preprocessing

## Communication Template

### When Asked the Problem
```
Interviewer: "Count subarrays with sum equal to k."

You: "Let me clarify:
- Contiguous subarray? [Yes]
- k is a fixed target? [Yes]
- Can array change? [No, immutable]

I see two approaches:
1. Brute force O(n²): check all pairs
2. Prefix sum O(n): precompute cumsum, use hash map

I'll use the prefix sum approach because 
it gives O(n) time with single pass."
```

### When Explaining Your Approach
```
"Here's my strategy:

1. **Precompute:** Build prefix sum array where prefix[i] = sum of [0, i)
2. **Key Insight:** sum(i, j) = prefix[j+1] - prefix[i]
3. **Transform Problem:** 
   - Want sum(i, j) = k
   - Equivalent: prefix[j+1] - prefix[i] = k
   - Rearrange: prefix[i] = prefix[j+1] - k
4. **Hash Map Tracking:** 
   - For each position, count how many prior prefixes equal (current - k)
   - Each count = one valid subarray ending here
5. **Complexity:** O(n) time (build + query), O(n) space (hash map)"
```

### When Showing the Code
```python
# Explain as you write:
prefix_sum_count = {0: 1}  # Initialize: sum 0 seen once
current_sum = 0
count = 0

# "Iterate through array, building running sum"
for num in nums:
    current_sum += num
    
    # "How many subarrays ending here sum to k?"
    # "Need prefix[i] = current_sum - k"
    if current_sum - k in prefix_sum_count:
        count += prefix_sum_count[current_sum - k]
    
    # "Record this prefix sum for future matches"
    prefix_sum_count[current_sum] = prefix_sum_count.get(current_sum, 0) + 1

return count
```

## The Core Insight

**The Magic of Prefix Sum:**

Naive: Check all O(n²) subarrays
```
for i in range(n):
    for j in range(i, n):
        if sum(array[i:j+1]) == k:
            count += 1  # O(n²)
```

Optimized: One pass with prefix math
```
for j in range(n):
    if (target_prefix) in seen_prefixes:
        count += seen_prefixes[target_prefix]  # O(1) lookup
```

**Why it works:**
- Prefix sum linearizes the problem
- Hash map converts search into counting
- Avoids redundant computations

## Variations & Pressure Test

**Interviewer:** "What if the array changes? Can you still solve it?"

Your Response:
- "Prefix sum assumes static array."
- "For updates, use Segment Tree or Fenwick Tree."
- "Trade-off: static→fast query, dynamic→more complex."

**Interviewer:** "Can you solve this in O(1) space?"

Your Response:
- "For this specific problem: No, we need hash map to count prefixes."
- "Trade-off: O(n) space for O(n) time vs. O(n²) time."
- "If we only needed one query (not counting), we could do better."

**Interviewer:** "How about a 2D matrix range sum?"

Your Response:
- "Extend to 2D: build 2D prefix array where prefix[i][j] = sum of rectangle [0,0] to [i,j]."
- "Query in O(1) using inclusion-exclusion: four prefix values."
- "Same principle, applied to 2D."

**Interviewer:** "What if sum constraint is 'at most k' instead of 'equals k'?"

Your Response:
- "Different approach needed."
- "Could use sliding window if all positive (monotonic property)."
- "Or sort prefixes and use binary search (changes structure)."

**Interviewer:** "Why hash map and not array?"

Your Response:
- "Hash map: works for any sum values (negative, large, etc.)"
- "Array: only works if sums fit in array bounds (restrictive)."
- "For interview, hash map is safer and more general."

## Common Mistakes to Avoid

### Mistake 1: Forgetting {0: 1} Initialization
```python
# ❌ WRONG
prefix_count = {}
for num in nums:
    current_sum += num
    # Misses subarrays starting at index 0

# ✓ CORRECT
prefix_count = {0: 1}  # sum 0 = subarray from start
```

**Why it matters:** Without this, subarrays like [k] starting at index 0 are missed.

### Mistake 2: Updating Hash Map Before Checking
```python
# ❌ WRONG
prefix_count[current_sum] += 1
if current_sum - k in prefix_count:
    count += prefix_count[current_sum - k]

# ✓ CORRECT
if current_sum - k in prefix_count:
    count += prefix_count[current_sum - k]
prefix_count[current_sum] += 1
```

**Why it matters:** Updating before checking can cause double-counting.

### Mistake 3: Confusing "Prefix" Definitions
```python
# Confusion: Does prefix[i] include nums[i] or not?

# Define clearly in code:
# Option 1: prefix[i] = sum of nums[0:i] (NOT including nums[i])
# Option 2: prefix[i] = sum of nums[0:i+1] (including nums[i])

# Stick to one definition consistently
# Option 1 is easier for math: range_sum(i,j) = prefix[j+1] - prefix[i]
```

### Mistake 4: Handling Duplicates Wrong
```python
# ❌ WRONG — overwrites count
prefix_count[current_sum] = 1

# ✓ CORRECT — accumulates count
prefix_count[current_sum] = prefix_count.get(current_sum, 0) + 1
```

### Mistake 5: Wrong Formula for Range Sum
```python
# ❌ WRONG
range_sum = prefix[j] - prefix[i]  # Off by one

# ✓ CORRECT
range_sum = prefix[j+1] - prefix[i]  # Sum of [i, j]
```

## Problem Variant Categories

### Category 1: Exact Sum Queries
- Count subarrays with sum = k
- First subarray with sum = k
- **All use:** Hash map to count prefix matches

### Category 2: Sum Range Queries
- Range [i, j] sum in O(1)
- Multiple queries on static array
- **All use:** Precomputed prefix array, simple math

### Category 3: 2D Operations
- 2D matrix range sum
- Rectangular region queries
- **All use:** 2D prefix array, inclusion-exclusion principle

### Category 4: Modular Arithmetic
- Subarrays with sum % k = specific value
- Tracks remainders instead of exact sums
- **All use:** Same prefix-counting logic

## The Checklist

Before submitting your answer:

- [ ] Explained why prefix sum applies here
- [ ] Clarified prefix definition (what's included/excluded)
- [ ] Justified initialization ({0: 1})
- [ ] Showed order of operations (check before update)
- [ ] Tested edge cases (single element, empty result, all valid)
- [ ] Confirmed O(n) time, O(n) space
- [ ] Verified hash map vs array choice

## Interview Narrative

**Strong narrative:**
> "I notice this is a subarray sum problem. 
> Rather than checking O(n²) subarrays, I'll use prefix sums.
> If sum(i, j) = k, then prefix[j+1] - prefix[i] = k,
> which means prefix[i] = prefix[j+1] - k.
> I track prior prefixes in a hash map to count matches.
> This gives O(n) time in single pass."

**Weak narrative:**
> "I'll just add everything up and... use a hash map... 
> and track prefix sums..."

## Green Flag Responses

- "Here's why this structure enables prefix sums..."
- "The key transformation is: [show math]"
- "I initialize {0:1} because [explain why]"
- "Hash map lets me avoid O(n²) by..."

## Red Flag Responses (Avoid)

- "I'm just following a template"
- "I use hash map because that's what you do"
- "This might not be right, but..."
- "I'll just try this approach"

## When Prefix Sum Does NOT Work

**Important to know when to pivot:**

```
Problem: "Range [i,j] sum with range updates"
❌ Prefix sum breaks with updates
✅ Use Segment Tree or Fenwick Tree instead

Problem: "Online queries with unknown array ahead of time"
❌ Can't precompute prefix sums
✅ Use data structure that handles dynamic updates

Problem: "2D matrix with updates"
❌ 2D prefix sum doesn't support updates
✅ Use 2D Segment Tree or Fenwick Tree
```

## Final Confidence Check

Before saying "I'm done":

1. **Correctness:** Does it pass all edge cases you can think of?
2. **Complexity:** O(n) time guaranteed by single pass?
3. **Space:** O(n) is acceptable for counting?
4. **Clarity:** Could someone maintain this code?

If yes to all, you're ready.

## Related Interview Content
- [[Coding Interviews]] — General interview strategies
- [[Communicating Thought Process]] — How to articulate solutions
- [[Prefix Sum]] — Pattern deep dive
- [[Prefix Sum Representative Problems]] — Practice problems
- [[Difference Array]] — Inverse pattern for range updates


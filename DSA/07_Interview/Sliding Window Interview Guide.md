---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/sliding-window]
canonical: true
related: [[[Sliding Window]], [[Coding Interviews]], [[Communicating Thought Process]]]
---
# Sliding Window Interview Guide

This guide prepares you to communicate and defend Sliding Window solutions in technical interviews.

## Pattern Recognition (30 seconds)
When you see a problem, ask yourself:

**Green Flags for Sliding Window:**
- "Subarray" or "substring" with constraint
- "At most k", "at least k", "exactly k" language
- Looking for optimal (min/max length, value)
- Problem reduces to O(n) if you avoid recomputation
- Constraint allows window boundaries to shift independently

**Yellow Flags (may be Sliding Window, may not):**
- "Sorted" — could be binary search instead
- "Pair" language — could be two pointers
- Matrix operations — could be prefix sum

**Red Flags (probably NOT Sliding Window):**
- "Subsequence" (non-contiguous) — dynamic programming
- "All pairs" — different approach needed
- No "contiguous" constraint — wrong pattern

## Communication Template

### When Asked the Problem
```
Interviewer: "Find the longest substring with all unique characters."

You: "Let me clarify:
- Substring must be contiguous? [Yes]
- What do I return, length or indices? [Length is fine]
- Can I use extra space? [Minimize it]

This looks like Sliding Window. The constraint (all unique) 
defines when the window is valid. I'll expand right, 
contract left when violated, and track the maximum."
```

### When Explaining Your Approach
```
"Here's my strategy:

1. **Pointers:** left and right boundaries of current window
2. **Invariant:** Window [left, right] satisfies the constraint
3. **Two Phases:**
   - Expand (right pointer): include new character
   - Contract (left pointer): remove until constraint satisfied again
4. **State:** Track what we need to maintain constraint 
   (frequency map, sum, etc.)
5. **Complexity:** O(n) time (each pointer moves once), 
   O(k) space (k = state size)"
```

### When Showing the Code
```python
# Explain as you write:
from collections import defaultdict

char_count = defaultdict(int)
left = 0
max_length = 0

# "Expand right pointer through entire string"
for right in range(len(s)):
    ch = s[right]
    char_count[ch] += 1
    
    # "Contract left pointer while constraint violated"
    while len(char_count) > k:  # More than k distinct
        char_count[s[left]] -= 1
        if char_count[s[left]] == 0:
            del char_count[s[left]]
        left += 1
    
    # "Update answer while window valid"
    max_length = max(max_length, right - left + 1)

return max_length
```

## The Two Phases (Critical Concept)

### Phase 1: Expand
- Right pointer always moves forward
- Add new element to window state
- Check if constraint still satisfied

### Phase 2: Contract
- Left pointer moves when constraint violated
- Remove element from window state
- Update best answer before continuing

**Key insight:** Contraction is not a separate loop! 
It's embedded: expand with right, contract while needed, repeat.

## Variations & Pressure Test

**Interviewer:** "What if we needed EXACTLY k, not AT MOST k?"

Your Response:
- "At most k is easier (subtract cases)."
- "Exactly k = (at most k) - (at most k-1)"
- "Or track left boundaries differently with two separate windows."

**Interviewer:** "Can you solve this without the extra space?"

Your Response:
- "For fixed-size window: just track running sum/product, O(1) space. ✓"
- "For variable window: need to track state (frequencies). O(k) minimum."
- "Trade-off: space vs. elegance."

**Interviewer:** "How do you know when to contract?"

Your Response:
- "Define the invalid condition clearly."
- "Contract WHILE condition holds (not IF)."
- "Example: `while sum > target`, not `if sum > target`."
- "WHILE avoids missing valid windows after contraction."

**Interviewer:** "What if the window can be empty?"

Your Response:
- "Initialize differently."
- "Check bounds: `if left <= right`."
- "Handle edge case where no valid window exists."

**Interviewer:** "Can you do this in one pass?"

Your Response:
- "Yes! Both pointers move monotonically forward."
- "Right pointer goes through entire array once: n moves."
- "Left pointer moves through array once: n moves total."
- "Total: 2n = O(n), not O(n²) because each move is final."

## Common Mistakes to Avoid

### Mistake 1: Recomputing Window State From Scratch
```python
# ❌ WRONG — recomputes for every position
for right in range(len(s)):
    for i in range(left, right+1):  # O(n²)!
        # check constraint

# ✓ CORRECT — incremental updates
for right in range(len(s)):
    add s[right] to state
    while constraint_violated:
        remove s[left] from state
        left += 1
```

### Mistake 2: Forgetting to Contract
```python
# ❌ WRONG — window keeps expanding, never shrinks
for right in range(len(s)):
    window_count[s[right]] += 1
    max_length = max(max_length, right - left + 1)

# ✓ CORRECT — contract when needed
for right in range(len(s)):
    window_count[s[right]] += 1
    while constraint_violated:
        window_count[s[left]] -= 1
        left += 1
    max_length = max(max_length, right - left + 1)
```

### Mistake 3: Wrong Contraction Condition
```python
# ❌ WRONG — contracts too much
if window_sum > target:
    left += 1

# ✓ CORRECT — contracts until constraint satisfied
while window_sum > target:
    left += 1
```

### Mistake 4: Updating Answer at Wrong Time
```python
# ❌ WRONG — misses answer during contraction
while constraint_violated:
    left += 1
max_length = max(max_length, right - left + 1)  # Too late!

# ✓ CORRECT — update before and after contraction
max_length = max(max_length, right - left + 1)
while constraint_violated:
    left += 1
    max_length = max(max_length, right - left + 1)
```

### Mistake 5: Confusing Expansion vs. Contraction Logic
```python
# ❌ WRONG — same condition for both
while left <= right:
    if constraint_ok:
        right += 1
    else:
        left += 1

# ✓ CORRECT — clear two-phase separation
for right in range(len(s)):
    # Phase 1: Expand
    add_to_window(s[right])
    
    # Phase 2: Contract
    while constraint_violated:
        remove_from_window(s[left])
        left += 1
    
    # Answer check
    update_answer()
```

## Pattern Variations Matrix

| Variant | Fixed/Variable | Constraint | Contraction |
|---------|---|---|---|
| Fixed Window (k) | Fixed | All operations in window | N/A |
| At Most k | Variable | count(uniques) ≤ k | count > k |
| At Least k | Variable | count(uniques) ≥ k | count < k |
| Sum Equals k | Variable | sum == k | Use subarray tracking |
| Min/Max In Window | Fixed | Size = k | Drop old, add new |

## State Tracking Cheat Sheet

```python
# Frequency tracking (characters, counts, etc)
from collections import Counter, defaultdict
window = defaultdict(int)
target = Counter(chars_needed)

# Sum tracking (numeric windows)
current_sum = 0
required_sum = target

# Set tracking (uniqueness)
unique = set()

# Index tracking (positions)
indices = deque()
```

## Practice Problems By Category

### Category 1: Frequency Constraints
- Longest Substring with At Most K Distinct
- Longest Repeating Character Replacement
- Permutation in String
- Longest Substring Without Repeating

### Category 2: Sum/Product Constraints  
- Maximum Sum Subarray of Size K
- Minimum Window Substring
- Subarray Product Less Than K

### Category 3: Character Constraints
- Count Distinct Subarrays with K Different Integers
- Substring with Concatenation of All Words
- Repeating Substring Pattern

## The Checklist

Before submitting your answer:

- [ ] Clearly stated the window constraint
- [ ] Explained expansion vs. contraction phases
- [ ] Verified WHILE (not IF) for contraction
- [ ] Tested edge cases (empty string, single char, all same)
- [ ] Confirmed O(n) time with monotonic pointers
- [ ] Showed why brute-force is worse
- [ ] Verified answer is updated at right time

## Interview Narrative

**Strong narrative:**
> "I recognize this as Sliding Window because [constraint]. 
> The window is valid when [condition]. 
> I expand with right pointer and contract when [invalid condition]. 
> Each pointer moves at most n times total, so O(n) time. 
> I track [state] for O(k) space."

**Weak narrative:**
> "I'll use two pointers and move them around... 
> left moves when sum is too big, right keeps moving..."

## Green Flag Responses (Demonstrate These)

- "Here's why this constraint enables Sliding Window..."
- "During expansion: [what happens]. During contraction: [what happens]."
- "I use WHILE not IF because I need to shrink until [condition]..."
- "Test cases: [fixed window], [empty result], [full array valid]"

## Red Flag Responses (Avoid These)

- "I think I just apply two pointers here" → Show pattern recognition
- "I move left when... uh, I'm not sure" → Have reasoning
- "This might be O(n²)" → Be confident in O(n) analysis
- "Let me just code and see" → Have strategy first

## Final Confidence Check

Before saying "I'm done":

1. **Pattern Match:** Why is this Sliding Window, not other patterns?
2. **Correctness:** Test with [empty], [single], [all valid], [none valid]
3. **Complexity:** Confirm O(n) time, justify pointer monotonicity
4. **Edge Cases:** Empty window, window size 1, full array

If yes to all four, you're ready to submit.

## Related Interview Content
- [[Coding Interviews]] — General interview strategies
- [[Communicating Thought Process]] — How to articulate solutions
- [[Whiteboarding]] — Writing code on a board
- [[Sliding Window]] — Pattern deep dive
- [[Sliding Window Representative Problems]] — Practice problems


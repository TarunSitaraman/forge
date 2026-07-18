---
type: problem
status: stable
tags: [dsa/problem, dsa/sliding-window, dsa/hash-map]
canonical: true
related: [[[Sliding Window]], [[Hash Map]], [[Python - Sliding Window]]]
difficulty: hard
---
# Sliding Window - Minimum Window Substring

## Problem Statement
Given two strings `s` and `t`, find the **minimum window substring** of `s` that contains all characters in `t` (including duplicates).

**Constraints:**
- Return the substring itself (not length)
- If no valid window exists, return empty string
- Window must be contiguous in `s`
- All characters of `t` (with frequencies) must be in window

## Classification
- **Patterns:** [[Sliding Window]], [[Hash Map]]
- **Template:** [[Python - Sliding Window]]
- **Category:** Minimum-length window with frequency matching constraint

## Pattern Selection
This is **hard-level** canonical Sliding Window because:
1. Requires tracking character frequencies (not just presence/absence)
2. Contraction condition is non-obvious: contract while window is valid
3. Updates must distinguish between: window not yet valid, valid but not minimal, validated
4. Return the actual substring with careful index tracking

This problem tests complete understanding of sliding window mechanics.

## Why Sliding Window Works
**Invariant:** Track [left, right] window such that all characters of `t` are contained.

**Pointer Movement:**
- **Right pointer:** Expand until we have all characters of `t`
- **Left pointer:** Contract while window still valid; track minimum as we contract
- Once left moves, we can never return to that position (monotonic property)

**State Tracking:**
- `t_count`: Frequency of each character in `t`
- `window_count`: Frequency of each character in current window [left, right]
- `formed`: Count of unique characters in current window with required frequency
- `required`: Count of unique characters we need (characters in `t` with count > 0)

**Efficiency Insight:** Only contract when window is valid; this avoids re-expanding.

## Python Solution
```python
def min_window_substring(s: str, t: str) -> str:
    """
    Find minimum window substring containing all characters from t.
    Time: O(n + m) where n=len(s), m=len(t)
    Space: O(1) bounded by alphabet size
    """
    if not s or not t or len(s) < len(t):
        return ""
    
    # Count characters needed
    t_count = {}
    for ch in t:
        t_count[ch] = t_count.get(ch, 0) + 1
    
    required = len(t_count)  # Number of unique chars we need
    formed = 0  # Number of unique chars with desired frequency
    window_counts = {}
    
    # left, right for window; best indices for answer
    left = 0
    best_len = float('inf')
    best_left, best_right = 0, 0
    
    for right in range(len(s)):
        ch = s[right]
        window_counts[ch] = window_counts.get(ch, 0) + 1
        
        # If this character's frequency now matches requirement
        if ch in t_count and window_counts[ch] == t_count[ch]:
            formed += 1
        
        # Contract window while it's valid (has all required chars)
        while left <= right and formed == required:
            ch = s[left]
            
            # Update best window if current is smaller
            if right - left + 1 < best_len:
                best_len = right - left + 1
                best_left, best_right = left, right
            
            # Remove left character from window
            window_counts[ch] -= 1
            
            # If this char's frequency now below requirement
            if ch in t_count and window_counts[ch] < t_count[ch]:
                formed -= 1
            
            left += 1
    
    # Return the minimum window substring
    return s[best_left:best_right + 1] if best_len != float('inf') else ""
```

## Reasoning
1. **Initialization:**
   - Build frequency map of `t`
   - Set `required` = number of unique characters in `t`
   - Initialize window trackers

2. **Expand Phase (right pointer):**
   - Add character at right position to window
   - Update window frequency count
   - If new character's frequency matches requirement, increment `formed`

3. **Contract Phase (left pointer):**
   - While window is valid (`formed == required`):
     - Record if current window is better than previous best
     - Try to shrink from left to find minimum
     - Remove left character from window
     - If removal breaks requirement, decrement `formed`, exit contraction

4. **Return:** Best window found (or empty string if none exists)

## Complexity Analysis
- **Time:** O(n + m)
  - Build `t_count`: O(m)
  - Right pointer: O(n) total movements
  - Left pointer: O(n) total movements (monotonic)
  - Each operation: O(1)
  - Total: O(n + m)

- **Space:** O(1) bounded
  - `t_count`: max 26 for lowercase, 128 for ASCII
  - `window_counts`: same bounds
  - Not O(n) space

## Edge Cases & Validation
```python
# No valid window
assert min_window_substring("a", "b") == ""

# Exact match
assert min_window_substring("ab", "ab") == "ab"

# Window at start
assert min_window_substring("adobecodebanc", "abc") == "banc"

# Window at end
assert min_window_substring("acbbaca", "aba") == "baca"

# Duplicates in target
assert min_window_substring("aaaaaaaaaaaabbbbbcdd", "abcdd") == "abbbbbcdd"

# Single character
assert min_window_substring("a", "a") == "a"
```

## Common Mistakes
1. **[[Boundary Errors]]**: 
   - Using `window_count[ch] > t_count[ch]` instead of `== t_count[ch]` for `formed` increment
   - Condition must check exact match, not just "at least"

2. **[[Off-by-One]]**:
   - Window length: `right - left + 1` not `right - left`
   - Slice: `s[best_left:best_right + 1]` not `s[best_left:best_right]`

3. **Early Contraction:** Contracting before window is valid
   - Must expand until `formed == required` first
   - Only then contract to find minimum

4. **Not Tracking Best Window:** Overwriting answer during contraction
   - Must record indices before each contraction attempt
   - Best answer might not be at the very end

5. **Condition for `formed` Update:**
   - Only update when character is in `t`
   - Extra characters in `s` shouldn't affect `formed`

## Interview Walkthrough
**Interviewer:** "Find minimum window in s that contains all characters from t."

**Your Response:**
1. "I'll use two pointers to maintain a sliding window."
2. "First, I expand right until I have all required characters."
3. "Then I contract left while window remains valid, tracking the minimum."
4. "I use frequency maps to know when I have all characters with correct counts."

**Diagram It:**
```
s = "adobecodebanc"
t = "abc"

Expand right:          [a d o b e c o d e b a n c]
                        ^ 
(need a=1, b=1, c=1)

At index 5 (c):        [a d o b e c...]
                        ^   ^
Valid! Window = "adobec"

Contract left:         [a d o b e c...]
                            ^   ^
Remove 'a': "dobecp". Now missing 'a'. Can't contract more.

Continue expanding...

Eventually find:       [b a n c]
Minimum window = "banc"
```

**Why This Resonates:**
- Shows you understand **two phases:** expand then contract
- Demonstrates tracking frequencies, not just presence
- Explains why `formed` variable avoids checking all characters each time
- Single-pass O(n) awareness vs. brute-force O(n²) or worse

## Variations
1. **Minimum Window Sequence:**
   - Characters must appear in `t`'s order (non-contiguous)
   - Still sliding window but track sequence position

2. **Longest Window with K Distinct:**
   - Track number of unique characters instead
   - Expand until > k uniques, contract until ≤ k

3. **Minimum Window with Duplicates Limited:**
   - Add constraint that each character can appear at most k times
   - Check `window_count[ch] <= k` during expansion

## Interview Prep Notes
- **Visualization:** Draw evolving window and character frequency maps
- **Proof Question:** "Why is left pointer monotonic? Could we ever go back left?"
- **Complexity Justification:** Why O(n) not O(n²)?
- **State Machine:** When do we expand? When do we contract? When is answer final?

## Related Problems
- [[Sliding Window - Longest Substring Without Repeating Characters]] (simpler uniqueness variant)
- [[Sliding Window - Longest Substring With At Most K Distinct Characters]] (maximum-length variant)
- [[Sliding Window - Permutation in String]] (simpler frequency matching)
- [[Sliding Window Representative Problems]]

## Related Concepts
- [[Sliding Window]] — advanced two-phase pattern with contraction
- [[Hash Map]] — efficient frequency tracking
- [[Time Complexity]] — O(n) two-pointer vs O(n²) brute force
- [[Greedy]] — why expanding to valid state then contracting finds minimum


---
type: problem
status: stable
tags: [dsa/problem, dsa/sliding-window, dsa/hash-map]
canonical: true
related: [[[Sliding Window]], [[Hashing]], [[Python - Sliding Window]]]
difficulty: medium
---
# Sliding Window - Longest Substring Without Repeating Characters

## Problem Statement
Given a string, find the length of the **longest contiguous substring** that contains no duplicate characters.

**Constraints:**
- Substring must be contiguous
- All characters within substring must be unique
- Return the length (not the substring itself)

## Classification
- **Patterns:** [[Sliding Window]], [[Hash Map]]
- **Template:** [[Python - Sliding Window]]
- **Category:** Variable-size window with frequency constraint

## Pattern Selection
This is canonical for Sliding Window because:
1. Expands window by adding characters to the right
2. Contracts from the left when duplicate detected
3. Never re-expands beyond maximum width (optimal answer is found once)
4. Hash map enables O(1) duplicate detection
5. Time complexity is O(n) single pass, not O(n²) brute force

## Why Sliding Window Works
**Invariant:** All characters in [left, right] are unique.

**Pointer Movement:**
- **Right pointer:** Always advances (consume next character)
- **Left pointer:** Moves only when duplicate detected
  - **Key insight:** Move left to `last_position_of_duplicate + 1`
  - This removes the duplicate in one move (not one step at a time)

**State Tracking:** Hash map stores `character → last_seen_position`
- Enables O(1) check: "Is this character already in current window?"
- Enables O(1) recovery: "Where do I need to start to avoid the duplicate?"

## Python Solution
```python
def length_of_longest_substring(s: str) -> int:
    """
    Find longest substring with all unique characters.
    Time: O(n) single pass through string
    Space: O(min(n, alphabet_size)) for character map
    """
    char_index = {}  # Maps character to its last seen position
    left = 0
    max_length = 0
    
    for right in range(len(s)):
        ch = s[right]
        
        # If character is in current window, move left boundary
        if ch in char_index and char_index[ch] >= left:
            # Move left past the previous occurrence
            left = char_index[ch] + 1
        
        # Update last seen position of current character
        char_index[ch] = right
        
        # Calculate current window length and track maximum
        max_length = max(max_length, right - left + 1)
    
    return max_length
```

## Reasoning
1. **Initialize:** left = 0, empty map, max_length = 0
2. **Expand:** right pointer always moves forward through string
3. **Duplicate Detection:** When right pointer encounters character:
   - Check if character in map AND if its last position >= left
   - The condition `>= left` ensures we only care about duplicates in current window
4. **Contraction:** Move left to `last_position + 1` (skip the old occurrence)
   - This is O(1) jump, not O(n) linear search
5. **Update:** Record new position of character, track max length
6. **Termination:** Process ends when right reaches end of string

## Complexity Analysis
- **Time:** O(n)
  - Right pointer advances once through string: n iterations
  - Left pointer moves forward monotonically: n movements total
  - Each hash map operation: O(1)
  - Total: O(n) not O(n²)
- **Space:** O(min(n, k))
  - k = alphabet size (26 for lowercase letters, 128 for ASCII, etc.)
  - Never store more than min(string_length, unique_chars)

## Edge Cases & Validation
```python
# Empty string
assert length_of_longest_substring("") == 0

# Single character
assert length_of_longest_substring("a") == 1

# All unique
assert length_of_longest_substring("abcabcbb") == 3  # "abc"

# All same character
assert length_of_longest_substring("bbbbb") == 1

# Mixed patterns
assert length_of_longest_substring("pwwkew") == 3  # "wke" or "kew"

# Duplicate at end
assert length_of_longest_substring("au") == 2

# Digits and special chars
assert length_of_longest_substring("au1!@#$%^&") == 10
```

## Common Mistakes
1. **[[Boundary Errors]]**: Condition `last[ch] >= left` is crucial
   - Missing `>= left` causes issues with old occurrences outside current window
   - Using `last[ch] > left` skips the exact duplicate position

2. **[[Off-by-One]]**: Window length calculation `right - left + 1`
   - Common error: `right - left` (misses one character)
   - Common error: `right - left + 2` (counts one too many)

3. **[[Infinite Loop]]**: Moving left multiple steps at once
   - Correct: `left = last[ch] + 1` (one move)
   - Wrong: Manual loop `while left <= last[ch]` (unnecessary complexity)

4. **Updating Hash Map at Wrong Time:** Must update position AFTER contraction
   - Correct: Move left, then `char_index[ch] = right`
   - Wrong: `char_index[ch] = right` first, might use stale position

## Interview Walkthrough
**Interviewer:** "Find longest substring with no duplicate characters."

**Your Response:**
1. "I'll use a sliding window that expands right and contracts left as needed."
2. "I track character positions in a hash map for O(1) duplicate detection."
3. "When I encounter a duplicate, I move the left boundary past its previous occurrence."
4. "This avoids re-checking and achieves O(n) time with single pass."

**Why This Resonates:**
- Shows you recognize frequency/uniqueness → sliding window
- Explains how hash map avoids repeated work
- Demonstrates O(1) contraction jump (not linear search)
- Single-pass awareness shows algorithmic maturity

**Common Follow-Ups:**
- "What if we needed the actual substring, not just length?" (track start/end indices)
- "What if the alphabet is huge or unknown?" (hash map handles gracefully)
- "Can you do this with a set instead of hash map?" (yes, but requires O(n) loop to find new left)

## Variations
1. **Longest Substring with At Most K Distinct Characters:**
   - Track count of unique chars in window
   - Expand while count ≤ k, contract when count > k

2. **Repeating Character Replacement:**
   - "Make all characters the same with at most k replacements"
   - Track frequency; contract when (window_length - max_frequency) > k

3. **Return the Actual Substring:**
   - Track start/end indices of best substring
   - Return `s[start:end+1]` instead of length

4. **Permutation in String:**
   - Similar mechanics with frequency matching instead of uniqueness

## Interview Prep Notes
- **Whiteboard Practice:** Draw left/right pointers and hash map as state evolves
- **Edge Case Drill:** Empty string, single char, all same char, all unique
- **Variation Drill:** "At most k distinct" and "return substring"
- **Proof Question:** "Why is the O(1) jump safe? Could we skip valid substrings?"

## Related Problems
- [[Sliding Window - Minimum Window Substring]] (similar mechanics, different constraint)
- [[Sliding Window - Longest Substring With At Most K Distinct Characters]]
- [[Sliding Window - Permutation in String]]
- [[Sliding Window Representative Problems]]

## Related Concepts
- [[Sliding Window]] — canonical variable-window pattern
- [[Hash Map]] — enabling O(1) lookups in window state
- [[Time Complexity]] — O(n) vs O(n²) brute force analysis
- [[String Processing]] — character frequency tracking


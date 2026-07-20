---
type: problem
status: stable
tags: [dsa/problem, dsa/trie]
canonical: true
related: [[[Trie]], [[DFS]]]
difficulty: medium
---
# Trie - Longest Word with All Prefixes (Longest Word in Dictionary)

## Problem Statement
Given a list of words, find the longest word that can be built one
character at a time by other words in the list (i.e., every prefix of
the word is also a complete word in the list). If there's a tie, return
the lexicographically smallest.

**Constraints:**
- A word's every proper prefix must itself be a word in the input list
- 1 ≤ words.length ≤ 1000

## Classification
- **Patterns:** [[Trie]], [[DFS]]
- **Category:** Trie construction + validity-constrained traversal

## Pattern Selection
This is canonical for combining Trie construction with a traversal that
only descends through nodes marking **complete words** — a plain "does
this prefix exist" check (as in basic Trie search) isn't enough; every
step along the path must itself be a terminal word, not just a prefix.

## Why This Approach Works
**Key insight:** insert every word into a Trie as usual. Then DFS from
the root, but only continue into a child if that child node is itself
marked `is_end = True`. Any node reached this way represents a word
"buildable" one letter at a time from other complete words.

## Python Solution
```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

def longestWord(words: list[str]) -> str:
    """
    Find longest word buildable by adding one letter at a time,
    where every prefix along the way is also a complete word.
    Time: O(sum of word lengths) to build + O(sum of word lengths) to search
    Space: O(sum of word lengths) for Trie
    """
    root = TrieNode()

    for word in words:
        node = root
        for ch in word:
            if ch not in node.children:
                node.children[ch] = TrieNode()
            node = node.children[ch]
        node.is_end = True

    best = ""

    def dfs(node, path):
        nonlocal best
        if len(path) > len(best) or (len(path) == len(best) and path < best):
            best = path

        for ch in sorted(node.children):
            child = node.children[ch]
            if child.is_end:  # only descend through complete words
                dfs(child, path + ch)

    dfs(root, "")
    return best
```

## Reasoning
1. **Build Trie:** standard insertion, marking `is_end` for every
   complete word.
2. **Constrained DFS:** starting from root, only recurse into a child if
   that child itself completes a word — this enforces "every prefix
   along the path is a real word."
3. **Track best:** update whenever a longer valid word is found, or a
   lexicographically smaller one of equal length (processing children in
   sorted order handles the tie-break naturally if you take the *first*
   qualifying update at each length, or explicitly compare as shown).
4. **Root itself isn't a word:** the DFS only records `path` at nodes
   reached through the `is_end` gate, so the empty path never wins.

## Complexity Analysis
- **Time:** O(N·L) — N words of average length L to build the Trie, plus
  a full DFS bounded by the same total character count
- **Space:** O(N·L) — Trie node storage in the worst case (no shared
  prefixes)

## Edge Cases & Validation
```python
assert longestWord(["w","wo","wor","worl","world"]) == "world"
assert longestWord(["a","banana","app","appl","ap","apply","apple"]) == "apple"
assert longestWord(["abc"]) == ""  # "ab" and "a" aren't in the list
assert longestWord([]) == ""
```

## Common Mistakes
1. **Checking `startsWith` instead of `is_end` at every step:** finds
   words that merely share a prefix with other words, not words where
   every prefix is itself a *complete* word — wrong semantics entirely.
2. **Not sorting children before recursing:** breaks the
   lexicographically-smallest tie-break if relying on "first found wins"
   instead of explicit comparison.
3. **Forgetting the root is never a valid word itself:** must not
   initialize `best` in a way that treats the empty string as a valid
   answer when no words qualify.

## Interview Walkthrough
**Interviewer:** "Find the longest word buildable one letter at a time
from other words in the list."

**Your Response:**
1. "I'll build a Trie from all the words first."
2. "Then DFS from the root — but critically, I only descend into a child
   node if that child itself marks a complete word, not just any
   prefix."
3. "This directly encodes 'every prefix along the way must be a real
   word from the list.'"
4. "I track the longest path found, breaking ties lexicographically by
   processing children in sorted order."

## Variations
1. **Word Break:** related but different — checks if *one* target string
   can be segmented, not searching for the longest buildable word
   ([[Memoization - Word Break]])
2. **Implement Magic Dictionary:** Trie-based search allowing exactly one
   character mismatch

## Related Problems
- [[Trie - Implement Trie]] (foundational operations this builds on)
- [[Trie - Word Search II]] (Trie + grid DFS, different traversal domain)
- [[Trie Representative Problems]]

## Related Concepts
- [[Trie]] — constrained traversal through complete-word nodes only
- [[DFS]] — traversal mechanism


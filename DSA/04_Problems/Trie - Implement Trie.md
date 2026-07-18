---
type: problem
status: stable
tags: [dsa/problem, dsa/trie]
canonical: true
related: [[[Trie]], [[Hash Map]]]
difficulty: medium
---
# Trie - Implement Trie (Prefix Tree)

## Problem Statement
Implement a Trie with insert, search, and startsWith methods.

**Constraints:**
- insert(word): adds word to trie
- search(word): returns true if word exists in trie
- startsWith(prefix): returns true if any word starts with given prefix

## Classification
- **Patterns:** [[Trie]]
- **Category:** Tree-based prefix data structure design

## Pattern Selection
This is canonical for Trie because:
1. Character-by-character tree structure enables O(L) operations (L = word/prefix length)
2. Naturally supports prefix queries (unlike hash set which only supports exact match)
3. Space-efficient for large dictionaries with shared prefixes

## Why Trie Works
**Structure:** Each node represents one character; path from root to node represents a prefix. `is_end` flag distinguishes complete words from mere prefixes.

## Python Solution
```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()
    
    def insert(self, word: str) -> None:
        """Time: O(L) where L = word length"""
        node = self.root
        for ch in word:
            if ch not in node.children:
                node.children[ch] = TrieNode()
            node = node.children[ch]
        node.is_end = True
    
    def search(self, word: str) -> bool:
        """Time: O(L)"""
        node = self._find_node(word)
        return node is not None and node.is_end
    
    def startsWith(self, prefix: str) -> bool:
        """Time: O(L)"""
        return self._find_node(prefix) is not None
    
    def _find_node(self, s: str):
        node = self.root
        for ch in s:
            if ch not in node.children:
                return None
            node = node.children[ch]
        return node
```

## Reasoning
1. **TrieNode Structure:** Dict of children (character → next node) + is_end boolean flag
2. **Insert:** Traverse/create nodes for each character; mark final node as word end
3. **Search:** Traverse to find node; check is_end flag (must be exact word, not just prefix)
4. **StartsWith:** Traverse to find node; existence alone suffices (don't check is_end)

## Complexity Analysis
- **Time:** O(L) per operation, L = word/prefix length
- **Space:** O(total characters across all words) in worst case (no shared prefixes)

## Edge Cases & Validation
```python
trie = Trie()
trie.insert("apple")
assert trie.search("apple") == True
assert trie.search("app") == False  # "app" not inserted as complete word
assert trie.startsWith("app") == True  # But "app" IS a valid prefix

trie.insert("app")
assert trie.search("app") == True  # Now it's a complete word too
```

## Common Mistakes
1. **Confusing Search with StartsWith:** search() must check is_end; startsWith() doesn't
2. **Not Marking is_end Properly:** Missing this makes search() always return False
3. **Using Array Instead of Dict:** Array[26] works for lowercase-only; dict handles any character set

## Interview Walkthrough
**Interviewer:** "Implement a Trie."

**Your Response:**
1. "Each node represents one character; children map to next characters."
2. "is_end flag distinguishes 'this is a complete word' from 'this is just a prefix'."
3. "Insert/search/startsWith are all O(L) where L is string length."

## Related Problems
- [[Trie Representative Problems]]

## Related Concepts
- [[Trie]] — core data structure
- [[Hash Map]] — used for children storage


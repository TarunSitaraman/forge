---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/trie]
canonical: true
related: [[[Trie]], [[Coding Interviews]]]
---
# Trie Interview Guide

## Pattern Recognition
**Green Flags:** `prefix search`, `autocomplete`, `word dictionary`, multiple string prefix matching

## Communication Template
`Trie enables O(L) prefix search where L is word length, versus O(N*L) for checking N words individually. Each node represents one character; paths from root represent prefixes.`

## Core Mechanics
```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()
    
    def insert(self, word):
        node = self.root
        for ch in word:
            if ch not in node.children:
                node.children[ch] = TrieNode()
            node = node.children[ch]
        node.is_end = True
    
    def search(self, word):
        node = self._find(word)
        return node is not None and node.is_end
    
    def _find(self, prefix):
        node = self.root
        for ch in prefix:
            if ch not in node.children:
                return None
            node = node.children[ch]
        return node
```

## Common Mistakes
1. Not marking is_end properly (confuses prefix with complete word)
2. Using array instead of dict for children (wastes space for sparse alphabets)

## Practice Problems
- Implement Trie, Word Search II, Add and Search Word

## Checklist
- [ ] Correctly distinguishes prefix from complete word
- [ ] O(L) operations confirmed where L = word length

## Related
- [[Trie]]
- [[Trie Representative Problems]]

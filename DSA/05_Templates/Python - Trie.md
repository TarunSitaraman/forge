---
type: python-template
status: stable
tags: [dsa/template, python]
canonical: true
---
# Python - Trie

## Purpose
Reusable interview-ready Python template for [[Trie]].

## Complexity
- Time: Depends on operation and input size; document per problem.
- Space: Depends on auxiliary structures; document per problem.

## Code
``python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_word = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for ch in word:
            node = node.children.setdefault(ch, TrieNode())
        node.is_word = True

    def search(self, word):
        node = self.root
        for ch in word:
            if ch not in node.children:
                return False
            node = node.children[ch]
        return node.is_word
``

## Edge Cases
- Empty input
- Single-element input
- Duplicate values or repeated states
- Disconnected components when graph-shaped

## Common Mistakes
- [[Off-by-One]]
- [[Boundary Errors]]
- [[Missing Visited Set]]


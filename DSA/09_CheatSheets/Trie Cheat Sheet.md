---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/trie]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Trie Cheat Sheet

## Signals
Prefix search; autocomplete; word dictionary; multiple string prefix matching.

## Core Moves
- TrieNode: children dict + is_end flag
- Insert: traverse/create nodes per character
- Search: traverse, check is_end at final node
- StartsWith: traverse, don't check is_end (prefix only)

## Complexity
O(L) per operation, L = word length.

## Template Links
- Custom TrieNode implementation typical

## Watch For
- Not marking is_end (confuses prefix with complete word)
- Using array instead of dict for sparse alphabets

## Canonical Depth
See [[Trie]] for full explanation.

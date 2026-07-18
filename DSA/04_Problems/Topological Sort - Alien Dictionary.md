---
type: problem
status: stable
tags: [dsa/problem, dsa/topological-sort]
canonical: true
related: [[[Topological Sort]], [[Graph Traversal]]]
difficulty: hard
---
# Topological Sort - Alien Dictionary

## Problem Statement
Given a list of words from an alien language sorted lexicographically according to that language's unknown alphabet, derive the character ordering. Return any valid ordering, or empty string if no valid ordering exists (contradictory constraints).

**Constraints:**
- Words consist of lowercase English letters
- Words are sorted according to alien alphabet rules
- Must detect invalid/contradictory orderings

## Classification
- **Patterns:** [[Topological Sort]], [[Graph Traversal]]
- **Category:** Constraint extraction + topological ordering

## Pattern Selection
This is canonical for Topological Sort because:
1. Word ordering implies character precedence constraints (build a DAG)
2. Comparing adjacent words reveals ONE character ordering constraint each
3. Once graph built, standard topological sort determines valid character order
4. Cycle in constraint graph means contradictory ordering (invalid input)

## Why This Approach Works
**Key Insight - Extracting Constraints:**
- Compare each pair of adjacent words character by character
- First differing character reveals ordering: word1[i] comes before word2[i]
- Build directed graph: edge from earlier character to later character
- Topological sort of this graph gives valid alphabet ordering

**Edge Case to Handle:**
- If word1 is prefix of word2, that's valid (e.g., "app" before "apple")
- If word2 is prefix of word1 (wrong order), that's INVALID (e.g., "apple" before "app")

## Python Solution
```python
from collections import defaultdict, deque

def alienOrder(words: list[str]) -> str:
    """
    Derive alien alphabet ordering using topological sort.
    Time: O(C) where C = total length of all words
    Space: O(1) - at most 26 characters
    """
    # Initialize graph with all characters
    graph = defaultdict(set)
    in_degree = {c: 0 for word in words for c in word}
    
    # Build graph by comparing adjacent words
    for i in range(len(words) - 1):
        word1, word2 = words[i], words[i + 1]
        min_len = min(len(word1), len(word2))
        
        # Check for invalid case: longer word is prefix predecessor
        if len(word1) > len(word2) and word1[:min_len] == word2[:min_len]:
            return ""  # Invalid: "apple" before "app" is contradiction
        
        # Find first differing character
        for j in range(min_len):
            if word1[j] != word2[j]:
                if word2[j] not in graph[word1[j]]:
                    graph[word1[j]].add(word2[j])
                    in_degree[word2[j]] += 1
                break  # Only first difference matters
    
    # Kahn's algorithm for topological sort
    queue = deque([c for c in in_degree if in_degree[c] == 0])
    result = []
    
    while queue:
        char = queue.popleft()
        result.append(char)
        
        for neighbor in graph[char]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    # If we processed all characters, valid ordering found
    if len(result) == len(in_degree):
        return ''.join(result)
    else:
        return ""  # Cycle detected: invalid ordering
```

## Reasoning
1. **Extract All Unique Characters:** Initialize in-degree map for every character seen
2. **Build Constraint Graph:** Compare adjacent words, find first differing character, add edge
3. **Handle Invalid Prefix Case:** If shorter word should come first but doesn't, return empty
4. **Topological Sort (Kahn's):** Process characters with in-degree 0, decrementing neighbors
5. **Validate Complete Processing:** If not all characters processed, cycle exists (invalid)

## Complexity Analysis
- **Time:** O(C) where C is total character count across all words
  - Building graph: O(C) to compare all adjacent word pairs
  - Topological sort: O(V+E) where V ≤ 26, E ≤ 26² (bounded by alphabet size)

- **Space:** O(1) — at most 26 characters and their edges (bounded alphabet)

## Edge Cases & Validation
```python
# Simple valid case
words = ["wrt", "wrf", "er", "ett", "rftt"]
result = alienOrder(words)
# Valid orderings include "wertf" (multiple valid answers possible)

# Invalid: prefix contradiction
words = ["abc", "ab"]
assert alienOrder(words) == ""  # "abc" before "ab" is invalid

# Single word (trivial case)
words = ["abc"]
result = alienOrder(words)
assert set(result) == {'a', 'b', 'c'}

# Cycle in constraints (invalid)
words = ["a", "b", "a"]
assert alienOrder(words) == ""  # a<b and b<a is contradiction

# Valid prefix relationship
words = ["app", "apple"]
result = alienOrder(words)  # Should not error
```

## Common Mistakes
1. **Missing Prefix Contradiction Check:** Not detecting when longer word appears before its own prefix
2. **[[Boundary Errors]]:** Not handling min_len correctly, or missing break after first difference
3. **Not Initializing All Characters:** Missing characters that appear but have in-degree 0
4. **Multiple Edges Between Same Pair:** Should avoid duplicate edges (only first constraint matters between two characters)

## Interview Walkthrough
**Interviewer:** "Derive character ordering from sorted alien words."

**Your Response:**
1. "Each pair of adjacent words gives one ordering constraint—their first differing character."
2. "I'll build a directed graph where edge A→B means A comes before B."
3. "Then I run topological sort (Kahn's algorithm) to get valid character ordering."
4. "I need to handle a tricky edge case: if a longer word appears before its own prefix, that's invalid."

**Why This Resonates:**
- Shows the clever constraint-extraction insight (not obvious from problem statement)
- Explains the prefix edge case explicitly (common pitfall)
- Demonstrates clean application of topological sort to a non-obvious domain

## Variations
1. **Verify Alien Dictionary:** Given an ordering, verify if word list is sorted correctly (simpler validation)
2. **Minimum Insertions to Make Ordering Valid:** Different optimization objective
3. **Alien Dictionary with Multiple Valid Orders:** Return lexicographically smallest valid ordering (requires priority queue)

## Related Problems
- [[Topological Sort - Course Schedule]] (simpler topological sort application)
- [[Topological Sort Representative Problems]]

## Related Concepts
- [[Topological Sort]] — core algorithm applied here
- [[Graph Traversal]] — building and processing the constraint graph
- [[Kahn's Algorithm]] — BFS-based topological sort implementation


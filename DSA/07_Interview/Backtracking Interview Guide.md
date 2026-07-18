---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/backtracking]
canonical: true
related: [[[Backtracking]], [[Coding Interviews]], [[Communicating Thought Process]]]
---
# Backtracking Interview Guide

## Pattern Recognition (30 seconds)

**Green Flags:**
- "Generate all" permutations/combinations/subsets
- Constraint satisfaction (N-Queens, Sudoku)
- Explore decision tree with undo capability

**Red Flags:**
- Single optimal answer needed (not all solutions) → DP or Greedy might be better

## Communication Template

```
Interviewer: "Generate all valid combinations."

You: "I'll build the combination incrementally, exploring each choice.
At each step: choose an element, recurse, then un-choose (backtrack) 
to try the next option. This explores the full decision tree."
```

## Core Mechanics

```python
def backtrack(state, choices, result):
    if is_complete(state):
        result.append(state[:])  # Copy, not reference!
        return
    
    for choice in choices:
        if is_valid(choice, state):
            state.append(choice)      # Choose
            backtrack(state, choices, result)  # Explore
            state.pop()                # Un-choose (backtrack)
```

## Common Mistakes

1. **Appending reference instead of copy:** `result.append(state)` vs `result.append(state[:])`
2. **Forgetting to backtrack:** Missing the "undo" step corrupts subsequent exploration
3. **Not pruning early:** Checking validity too late wastes exploration time

## Practice Problems
- Permutations / Combinations
- N-Queens
- Word Search
- Subsets

## The Checklist
- [ ] Copy state when saving to results (not reference)
- [ ] Properly backtrack (undo) after each exploration
- [ ] Added pruning where possible for efficiency
- [ ] Handled duplicates correctly if input has them

## Related Content
- [[Backtracking]] — Pattern deep dive
- [[Backtracking Representative Problems]]


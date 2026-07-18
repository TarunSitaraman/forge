---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/debugging]
canonical: true
related: [[Pattern Index]], [[Algorithm Index]], [[Template Index]], [[Problem Index]]
---
# Debugging

## Purpose
This page captures durable interview guidance for Debugging. Keep it practical, concise, and linked to canonical DSA pages rather than repeating theory.

## Recognition Heuristics
- Identify the input structure before choosing an algorithm.
- Ask whether the data is sorted, dynamic, weighted, directed, sparse, or constrained by small bounds.
- Look for monotonicity, overlapping subproblems, graph structure, interval structure, or prefix-state reuse.

## Communication Strategy
State the brute force baseline, name the bottleneck, then introduce the pattern that removes it. Explain invariants before code and complexity after code.

## Common Interviewer Tricks
- Change inclusive ranges to half-open ranges.
- Add duplicate values.
- Ask for first/last occurrence rather than existence.
- Move from static input to streaming updates.
- Require path reconstruction, not only cost.

## Optimization Discussion
Use [[Time Complexity]], [[Space Complexity]], and [[Amortized Analysis]] to explain tradeoffs. If two approaches share the same asymptotic bound, compare constant factors, memory, implementation risk, and failure modes.

## Representative Questions
- Classify the problem using [[Pattern Index]].
- Select reusable code from [[Template Index]].
- Extract lessons and mistakes into [[Problem Index]] and [[Mistake Template]].

## Checklist
- [ ] Clarify constraints and input semantics
- [ ] State baseline and bottleneck
- [ ] Choose a canonical pattern
- [ ] Explain invariant or recurrence
- [ ] Walk through edge cases
- [ ] Analyze time and space
- [ ] Mention common bugs

## Related Pages
- [[Coding Interviews]]
- [[Communicating Thought Process]]
- [[Optimization]]
- [[Common Pitfalls]]


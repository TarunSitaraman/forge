# Competitive Programming — Pattern Recognition Pass

*Purpose:* Identify the actual algorithmic pattern a problem requires before writing any code, avoiding the common failure of coding a brute force that will TLE on the real constraints.

## When to use
Reading a new competitive programming problem, before writing code.

## Expected input
The problem statement, constraints (n, time limit), and any examples given.

## Expected output
The identified pattern/technique, the target complexity given constraints, and the specific reason a naive approach would fail.

## The complete prompt
```
Analyze this competitive programming problem before I code anything.

Problem: {statement}
Constraints: {n, time limit, memory limit}
Examples: {sample input/output}

Analyze:
1. From the constraints, derive the required time complexity (e.g. n up
   to 10^5 with a 1-2s limit implies O(n log n), not O(n^2)).
2. Identify the underlying pattern/technique this problem is testing
   (e.g. binary search on answer, DP on intervals, graph shortest path,
   two pointers) — name it specifically, don't describe the problem back
   to me.
3. State exactly why a naive/brute-force approach would fail given the
   constraints (TLE at what n, or MLE at what memory).
4. Identify the key insight or transformation that unlocks the intended
   complexity — the "aha" that turns brute force into the efficient
   approach.

Do not write code yet. First confirm the pattern and complexity target,
then I'll ask for implementation.
```

## Variants
- **Contest-speed variant:** skip straight to "pattern name + one-line why" for problems you recognize quickly, saving the full analysis for genuinely unfamiliar problems.
- **Post-contest review variant:** run this after solving (or failing to solve) to check whether you identified the right pattern or got lucky/stuck.

## Related prompts
- [[cp-implementation-checklist]]

## Tips
- Always derive the complexity target from constraints first — it's the single fastest filter for ruling out wrong approaches.
- Resist jumping to code before the pattern is confirmed; mid-contest rewrites cost more time than the extra minute of analysis.

## Common mistakes
- Starting to code a brute force "to see if it's fast enough" instead of computing the complexity bound from constraints upfront.
- Misidentifying the pattern because of surface-level similarity to a known problem, without checking the key insight actually applies.

## Example
Input: n up to 2×10^5, need to answer range-sum queries with point updates.
Expected output: derives that O(n log n) or O(n sqrt n) is needed, identifies Fenwick tree / segment tree as the pattern, explains why an O(n) per-query naive approach TLEs at this n and query count.

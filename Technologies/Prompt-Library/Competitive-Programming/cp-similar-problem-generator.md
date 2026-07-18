# CP Similar Problem Generator

*Purpose:* Get pointed toward real practice problems that drill the exact
pattern/insight you just learned, at a well-calibrated difficulty — for
reinforcement reps, not more hints.

## When to use
Right after solving a problem and extracting its insight (post-solution
analysis), when you want deliberate reps on that specific pattern.

## Expected input
The problem you just solved and its core insight, your current comfort
level with the pattern, and roughly how much harder/easier you want the
next rep to be.

## Expected output
A short list of real, specific problems (named or well-described enough
to search for) that drill the same core insight, at graduated
difficulty — not the same problem restated.

## The complete prompt
```
I just solved this and want more reps on the core insight — recommend
similar problems, not a rehash of this one.

Problem/insight: {problem statement + the transferable insight extracted}
My comfort level with this pattern: {shaky / solid but slow / confident}
Desired next difficulty: {similar / slightly harder / significantly harder}

Give me 3-4 problems (name them if you know specific ones on common
platforms, or describe them precisely enough to search for) that:
1. Require the SAME core insight, but in different surface disguise —
   not the same problem with different numbers.
2. Are graduated: one at similar difficulty to confirm the pattern is
   solid, one a step harder to test if it generalizes, and one that
   combines this pattern with something else (a realistic "how it
   actually shows up in a harder contest" case).
3. For each, state in one sentence WHY it uses the same core insight —
   so I can self-check that I've correctly identified the pattern rather
   than pattern-matching on surface similarity.

Do not give me the solution approach for any of them — just enough to
know they're a good next rep and go attempt them cold.
```

## Variants
- **Weak-pattern variant:** bias toward more problems at a similar (not harder) difficulty, to build confidence before increasing challenge.
- **Contest-prep variant:** bias toward problems that combine this pattern with others, matching realistic contest difficulty.

## Related prompts
- [[cp-post-solution-analysis]]
- [[cp-weakness-finder]]

## Tips
- State your honest comfort level — recommendations should differ meaningfully between "shaky" and "solid but slow."
- Insist on the one-sentence "why this uses the same insight" — it lets you self-check pattern recognition before diving in, which is itself part of the practice.

## Common mistakes
- Accepting recommendations that are really just the same problem with different flavor text, providing no real generalization test.
- Skipping straight to "significantly harder" reps before confirming the base pattern is actually solid.

## Example
Input: just solved a problem using binary search on the answer, comfort = "shaky."
Expected output: recommends 2 problems at similar difficulty using binary-search-on-answer in different domains (e.g. minimizing a maximum vs. maximizing a minimum), and one harder problem combining it with a greedy feasibility check.

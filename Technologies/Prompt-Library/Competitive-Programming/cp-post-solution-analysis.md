# CP Post-Solution Analysis

*Purpose:* Extract the real, transferable lesson from a problem you just
solved (or gave up on), so the insight generalizes to future problems
instead of being forgotten once this one's closed.

## When to use
Immediately after solving a problem — successfully, with help, or by
reading the editorial — while the reasoning is still fresh.

## Expected input
The problem, your solution (or the editorial's approach if you needed
it), and how you got there (unaided / with a hint / after reading the
editorial).

## Expected output
The precise transferable insight, an honest classification of the gap
(if any) between your approach and needing help, and a suggestion for
what to drill if a real gap was found — not a restatement of the
solution.

## The complete prompt
```
Help me extract the real lesson from this problem, now that I've solved it.

Problem: {statement}
My solution / the approach used: {code or approach}
How I got here: {unaided / after a hint — what hint / after reading editorial}

Do NOT just explain the solution back to me — I already have it. Instead:
1. State the transferable insight in one precise sentence — generalized
   enough that I'd recognize it in a different problem's disguise, not
   tied to this problem's specific story.
2. If I needed help: be honest about WHERE my reasoning diverged from
   what was needed — the specific step, not a vague "I didn't know the
   technique."
3. Classify the gap: was it a pattern-recognition gap (didn't see which
   technique applied), an implementation gap (knew the approach, struggled
   to code it), or a complexity-analysis gap (misjudged whether an
   approach would pass)?
4. Suggest ONE specific thing to drill based on the classification —
   not generic "practice more," but e.g. "solve 3 more problems using
   this exact DP state transition before moving on."
5. Ask me to state the insight back in my own words before we're done —
   don't let this end with just your explanation.
```

## Variants
- **Unsolved-then-editorial variant:** add explicit honesty check — did the editorial reveal something genuinely non-obvious, or something you could have derived with more time?
- **Speed-run variant:** for problems solved quickly and confidently, skip straight to "what made this fast for me" to reinforce the working instinct.

## Related prompts
- [[cp-hint-only-coaching]]
- [[cp-weakness-finder]]

## Tips
- Do this even for problems solved unaided and quickly — understanding WHY it was fast for you reinforces the right instinct.
- Always answer the "state it back in your own words" step for real; skipping it is the difference between passive reading and actual retention.

## Common mistakes
- Treating this as a request to re-explain the solution, which just re-reads what you already know instead of extracting new insight.
- Classifying every gap as "didn't know the technique" without checking whether it was actually an implementation or complexity-analysis failure instead.

## Example
Input: solved a DP problem after a hint about the state needing an extra dimension.
Expected output: transferable insight = "when a constraint restricts a resource over time, that resource usually needs to be part of the DP state," classified as a pattern-recognition gap, suggests 3 similar-shaped DP problems to drill next.

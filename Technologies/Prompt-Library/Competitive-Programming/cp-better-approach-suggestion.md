# CP Better Approach Suggestion

*Purpose:* After a correct solution, explore whether a cleaner or more
elegant approach existed — as a guided comparison that builds judgment,
not a "here's the optimal answer" reveal.

## When to use
After solving a problem, especially when the solution felt clunky,
overly complex, or you have a nagging sense a simpler approach existed.

## Expected input
Your accepted solution and approach, and your own guess about whether a
cleaner approach might exist.

## Expected output
A guided exploration of whether your approach was well-suited, with any
genuinely better approach surfaced through questions first — not handed
over immediately.

## The complete prompt
```
My solution works but felt {clunky / overly complex / like I'm missing a
cleaner approach}. Help me figure out if a better approach existed —
guide me toward it, don't just hand it over.

Problem: {statement}
My approach/code: {code}
My own guess: {do you suspect a specific inefficiency, or just a general
feeling?}

Process:
1. First, ask me a guiding question that points at the part of my
   solution most likely to have a cleaner alternative (e.g. "what's this
   nested loop actually computing — is there redundant recomputation
   across iterations?"). Don't answer it yourself yet.
2. Based on my response, either confirm my approach was actually
   reasonable for this problem (not everything needs to be more clever
   — say so honestly if true), or narrow toward the specific inefficiency
   with one more guiding question.
3. Only after I've had a real chance to respond to the guiding questions,
   reveal the cleaner approach if one genuinely exists — and explain WHY
   it's better (complexity, clarity, robustness) not just THAT it's
   different.
4. If my approach was already close to optimal, say so clearly — don't
   manufacture a "better" approach just to have something to suggest.
```

## Variants
- **Elegance-only variant:** for problems where both approaches have the same complexity, focus the comparison purely on clarity/maintainability rather than performance.
- **Multiple-approaches variant:** if 2+ genuinely different valid approaches exist, compare their tradeoffs rather than declaring one strictly better.

## Related prompts
- [[cp-code-review]]
- [[cp-complexity-review]]

## Tips
- Answer the guiding questions honestly and think before checking the reveal — the value here is in the attempt, not the final answer.
- Accept "your approach was already good" as a valid, useful outcome — this prompt isn't designed to always find something to improve.

## Common mistakes
- Skipping the guided-question step and asking directly "what's the best approach to this problem," turning this into a solution lookup.
- Assuming every accepted solution must have a "better" alternative and pushing for one even when the original approach was already appropriate.

## Example
Input: a solution using nested loops for a problem where two-pointer would work, feeling "clunky."
Expected output: asks "does your inner loop's work depend on anything that changes monotonically as the outer loop advances?" before revealing that a two-pointer approach eliminates the nested loop's redundancy.

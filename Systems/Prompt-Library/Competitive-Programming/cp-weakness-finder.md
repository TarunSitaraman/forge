# CP Weakness Finder

*Purpose:* Identify your real, current problem-solving weaknesses from
aggregate evidence (Problem Journal entries, Mistake Tracker, contest
history) — not a generic list of "things CP people should know."

## When to use
Periodically (weekly/monthly) or after a batch of problems/a contest, to
direct deliberate practice at the actual gap instead of whatever feels
interesting to practice.

## Expected input
A batch of Problem Journal entries and/or Mistake Tracker rows, and
recent contest reflections if available.

## Expected output
A ranked list of the 2-3 weaknesses with the strongest evidence behind
them, each with the specific evidence and a concrete practice
recommendation — not a broad self-improvement list.

## The complete prompt
```
Find my real current problem-solving weaknesses from this evidence —
don't give generic CP advice, ground everything in what's actually here.

Problem Journal entries: {paste several recent entries}
Mistake Tracker: {paste current rows}
Recent contest reflections (if any): {paste}

Analyze:
1. Look for a pattern across entries — not "you got this problem wrong,"
   but a recurring TYPE of failure (e.g. consistently misjudging when
   greedy fails, consistently under-considering the DP state space,
   consistently rushing implementation and introducing off-by-ones).
2. For each weakness found, cite the SPECIFIC entries/evidence that
   support it — don't state a weakness without pointing to where it
   showed up.
3. Distinguish weaknesses that are pattern-recognition gaps from ones
   that are implementation or complexity-analysis gaps — the practice
   fix differs by type.
4. Rank the top 2-3 by how much evidence supports them and how much
   they're likely costing you (recurring across many problems > a
   one-off).
5. For each, suggest a specific, boundable practice action (e.g. "solve
   5 problems specifically requiring 2D DP state this week") — not
   "get better at DP."

Do not surface a weakness you can't point to real evidence for in what
I gave you.
```

## Variants
- **Contest-only variant:** narrow input to contest reflections only, to find weaknesses specific to timed/pressure conditions vs. untimed practice.
- **Long-horizon variant:** run against months of journal entries to catch slow-moving weaknesses that a single week's data wouldn't reveal.

## Related prompts
- [[cp-post-solution-analysis]]
- [[cp-similar-problem-generator]]

## Tips
- Feed in real entries, not summaries of them — the specific reasoning-gap language in your own journal is the actual evidence this analysis needs.
- Revisit this periodically with fresh data; a weakness identified and drilled should eventually stop showing up in the evidence.

## Common mistakes
- Accepting a weakness diagnosis that isn't backed by a specific cited entry — that's the AI pattern-matching to generic CP wisdom instead of your real data.
- Trying to fix every surfaced weakness at once instead of focusing on the top 1-2 with the strongest evidence.

## Example
Input: 8 journal entries, 3 of which mention "didn't realize I needed to consider negative numbers."
Expected output: flags "under-considering edge cases in problem constraints, specifically sign/boundary conditions" as a top weakness, citing the 3 specific entries, recommends a focused set of edge-case-heavy problems.

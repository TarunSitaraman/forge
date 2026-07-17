# CP Complexity Review

*Purpose:* Understand exactly WHY your solution's complexity is what it
is — including catching hidden extra factors — so you build an accurate
mental model of cost, not just a pass/fail from the judge.

## When to use
After a solution passes (or TLEs) and you're not fully sure why, or want
to confirm your complexity reasoning was actually correct rather than
lucky.

## Expected input
The code, the constraints, and your own stated belief about its
complexity.

## Expected output
Confirmation or correction of your stated complexity, with the reasoning
made explicit — especially any hidden factor you missed — not just a new
number handed to you.

## The complete prompt
```
Review the complexity of this solution — but make me understand the
reasoning, don't just state a Big-O.

Code: {code}
Constraints: {n and limits}
My belief about its complexity: {your own analysis first — required}

Process:
1. First, confirm or correct my stated complexity. If I'm wrong, ask me
   a guiding question about the specific line/loop that reveals the
   error, rather than immediately stating the right answer.
2. Identify any hidden extra factor I likely didn't account for (e.g. a
   sort inside a loop, a linear-time operation on a data structure I
   assumed was O(1), string concatenation in a loop).
3. Confirm whether this complexity actually fits within the constraint
   table's target for the given n (see `Systems/Competitive-Programming/complexity-cheatsheet.md`).
4. If there's a hidden factor, ask me first whether I can see how to
   remove it, before telling me the fix directly.

I must state my own complexity belief before you respond — don't let me
skip straight to asking you to analyze it cold.
```

## Variants
- **TLE-diagnosis variant:** focus specifically on identifying the exact operation causing the TLE, using a guided-question approach rather than immediate diagnosis.
- **Space-complexity variant:** run the same process for memory instead of time, for MLE cases.

## Related prompts
- [[cp-post-solution-analysis]]
- [[cp-problem-pattern-recognition]]

## Tips
- Always state your own complexity belief first — this is what turns the exercise into calibration practice instead of passive lookup.
- Push for the guided-question step before accepting a direct answer; working it out yourself with a nudge builds the skill, being told doesn't.

## Common mistakes
- Skipping your own analysis and asking "what's the complexity of this" cold, turning a calibration exercise into an answer lookup.
- Accepting "it's O(n log n)" without understanding which specific operation contributes the log factor.

## Example
Input: code with a `.sort()` call inside a loop over n elements, your belief = "O(n log n) overall."
Expected output: asks "what's the complexity of the sort call, and how many times does it run?" before confirming the actual complexity is O(n² log n).

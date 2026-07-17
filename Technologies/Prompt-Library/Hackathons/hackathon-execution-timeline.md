# Hackathon Execution Timeline

*Purpose:* Convert a chosen hackathon idea into a concrete hour-by-hour execution plan with explicit checkpoints, so the team notices they're behind while there's still time to cut scope.

## When to use
Immediately after committing to a hackathon idea, before writing any code.

## Expected input
The idea and its scoped-down plan, total time available, team size/roles, and the demo time (hard deadline).

## Expected output
An hour-by-hour timeline with checkpoints, each checkpoint stating what must be true by then and the scope cut to make if it isn't.

## The complete prompt
```
Build an hour-by-hour execution timeline for this hackathon project.

Idea/scope: {idea and minimum viable scope}
Total time: {hours}
Team/roles: {who's doing what}
Hard demo deadline: {time}

Produce a timeline with:
1. Checkpoints every 3-4 hours, each stating what must be functionally
   true by that point (not "50% done" — a concrete capability, e.g. "core
   data pipeline running end to end, even with fake data").
2. At each checkpoint, the specific scope cut to make if it's not met —
   decided now, not improvised at 3am.
3. A hard buffer before the deadline (at least 10% of total time)
   reserved for demo prep and fixing something that breaks — not coding
   new features.
4. The riskiest unknown (from idea triage) scheduled to be validated in
   the FIRST checkpoint, not discovered late.

Output as a simple table: Time | Checkpoint goal | Cut-scope fallback.
```

## Variants
- **Multi-day hackathon variant:** add an explicit sleep/rest checkpoint — burnout is a real failure mode at 24h+ events.
- **Team-of-one variant:** compress checkpoints and bias the buffer even larger given no parallelization.

## Related prompts
- [[hackathon-idea-triage]]
- [[demo-script-design]]

## Tips
- Decide scope cuts in advance, at full mental capacity — deciding what to cut at 3am under fatigue produces worse decisions.
- Protect the demo-prep buffer fiercely; a working demo of a smaller scope beats a broken demo of the full vision.

## Common mistakes
- Leaving no buffer before the deadline, so the last hour is spent debugging instead of rehearsing the demo.
- Setting checkpoints as vague percentages ("50% done") instead of concrete functional capabilities that are easy to honestly assess.

## Example
Input: 24-hour hackathon, demo at hour 24, team of 3.
Expected output: checkpoints at hour 4 (riskiest unknown validated), hour 12 (end-to-end happy path working), hour 20 (feature-complete for minimum scope), hour 22-24 reserved for demo prep/fixes.

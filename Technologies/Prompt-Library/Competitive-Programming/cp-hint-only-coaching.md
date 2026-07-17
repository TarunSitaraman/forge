# CP Hint-Only Coaching

*Purpose:* Get unstuck on a problem via the smallest possible nudge,
never the solution — preserving the productive struggle that's where
actual skill is built.

## When to use
Stuck mid-problem, after a genuine timed attempt, before reaching for an
editorial.

## Expected input
The problem statement, your current approach/thinking so far (including
dead ends), and how long you've been stuck.

## Expected output
One small hint at the lowest level that could plausibly unstick you —
never the approach, never code, never "here's the key insight."

## The complete prompt
```
Act as a coach, not a solver. I'm stuck on this problem and want the
SMALLEST possible hint — not the approach, not the key insight spelled
out, just enough to get me thinking again.

Problem: {statement}
My thinking so far: {approach tried, where I got stuck}
Time stuck: {minutes}

Rules for your response:
1. Do NOT state the algorithm/pattern name.
2. Do NOT give a worked example that reveals the approach.
3. Give ONE of: a clarifying question about the problem I might be
   misreading, a suggestion to reconsider one specific assumption I
   seem to be making, or a prompt to think about a specific small case
   (without solving it for me).
4. Stop there. Do not chain into a fuller explanation even if it seems
   helpful — if I need more, I'll ask again.

If I ask again after trying your hint, give the NEXT smallest hint level
— slightly more specific, still not the answer. Only on a third request
should you get close to naming the pattern, and even then, make me derive
the application myself.
```

## Variants
- **Timed-practice variant:** add a hard rule refusing any hint until a stated minimum struggle time has passed.
- **Post-hint-still-stuck variant:** explicitly escalate hint specificity one level per request, tracked across the conversation.

## Related prompts
- [[cp-post-solution-analysis]]
- [[cp-problem-pattern-recognition]]

## Tips
- Always state how long you've actually been stuck — a coach prompt without this can't calibrate how much of a nudge is fair to give.
- If the first hint unsticks you, stop there and go solve — don't keep chatting with the AI once you have what you need.

## Common mistakes
- Asking a "hint" prompt in a way that's really asking for the approach with extra steps ("just tell me what data structure to use").
- Chaining hint requests back-to-back without actually attempting the problem again between them.

## Example
Input: stuck on a problem for 20 minutes, tried brute force, suspect it's too slow but don't know the fix.
Expected output: a question like "what happens to your brute force if you could answer each query in O(log n) instead of O(n) — what needs to be precomputed for that?" — not "use a Fenwick tree."

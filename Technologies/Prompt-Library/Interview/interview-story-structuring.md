# Behavioral Interview Story Structuring (STAR, Evidence-Checked)

*Purpose:* Structure a real work experience into a behavioral interview answer that's specific and evidence-backed, not a generic STAR template filled with vague claims.

## When to use
Preparing for behavioral interviews, especially for a specific competency you know will be probed (leadership, conflict, failure).

## Expected input
The real situation (rough, as you remember it), and the competency the question is probing for (e.g. "tell me about a conflict with a teammate").

## Expected output
A tight STAR-structured answer, with follow-up-proof specificity, and a flag on any part of the story that's vague and needs a real detail filled in.

## The complete prompt
```
Structure this real experience into a behavioral interview answer for the
competency: {competency, e.g. "conflict resolution," "dealing with failure"}

Raw situation (as I remember it): {rough description}

Structure as STAR, but hold it to this bar:
1. Situation/Task — one or two sentences max, just enough context to
   understand the stakes. Don't let this become a rambling preamble.
2. Action — the actual specific thing YOU did (not "we"), including a
   decision point where a different, worse choice was available and you
   didn't take it. This is the part interviewers probe hardest — make it
   concrete enough to survive a "why did you decide that" follow-up.
3. Result — the concrete outcome, with a number if one genuinely exists.
   If none exists, don't invent one — state the qualitative outcome
   honestly.
4. Reflection (one sentence) — what you'd do differently or what you
   learned, showing self-awareness without undermining the story's
   competency.

Flag any part of my raw situation that's currently too vague to survive a
follow-up question ("what specifically did you say," "what was the
teammate's reaction") so I can fill in the real detail before using this
in an interview.
```

## Variants
- **Failure/weakness variant:** weight the reflection section more heavily — for this competency, genuine self-awareness matters more than the outcome.
- **Leadership-without-authority variant:** emphasize the influence mechanism specifically (how you got buy-in without formal authority).

## Related prompts
- [[resume-bullet-impact-rewrite]]
- [[technical-interview-mock-session]]

## Tips
- Bring the real, messy memory of what happened — a story sanded down to a clean narrative before this pass loses the specificity interviewers probe for.
- Take the flagged vague spots seriously; those are exactly where a live follow-up question will expose an underprepared answer.

## Common mistakes
- Using "we" throughout the Action section, leaving the interviewer unable to tell what you specifically contributed.
- Inventing a clean number for Result when the real outcome was qualitative — a fabricated number that can't be defended under follow-up is worse than an honest qualitative one.

## Example
Input: competency = "conflict resolution," raw situation = "disagreed with a teammate about API design, eventually worked it out."
Expected output: flags this as too vague, asks what the actual disagreement was and what you specifically said/did to resolve it, before producing the structured answer.

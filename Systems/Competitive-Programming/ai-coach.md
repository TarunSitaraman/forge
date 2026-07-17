# AI Coach — Usage Guide

*Guidance: this page explains HOW to use an LLM as a competitive
programming coach without letting it become a solution vending machine.
The single governing rule across every prompt in this module: the AI
should make you think harder, not think for you.*

---

## The core rule

Never ask an AI for a solution directly. Every interaction should be
structured so the AI gives you the *next smallest useful nudge* — a
question, a reframing, a hint — and stops. If a response gives you the
full approach before you've had a real chance to find it yourself, that
interaction failed, regardless of whether the code was correct.

## When to reach for which prompt

| Situation | Prompt to use |
|---|---|
| Stuck mid-problem, want a nudge without spoiling it | `Systems/Prompt-Library/Competitive-Programming/cp-hint-only-coaching.md` |
| Just solved it, want to extract the real lesson | `Systems/Prompt-Library/Competitive-Programming/cp-post-solution-analysis.md` |
| Solution works, unsure if it's fast enough or why | `Systems/Prompt-Library/Competitive-Programming/cp-complexity-review.md` |
| Periodic check on what's actually holding you back | `Systems/Prompt-Library/Competitive-Programming/cp-weakness-finder.md` |
| Want more reps on a pattern you just learned | `Systems/Prompt-Library/Competitive-Programming/cp-similar-problem-generator.md` |
| Prepping this problem's pattern for an interview setting | `Systems/Prompt-Library/Competitive-Programming/cp-interview-variant.md` |
| Solution passed, want real code review (not just correctness) | `Systems/Prompt-Library/Competitive-Programming/cp-code-review.md` |
| Solution passed but felt clunky/inelegant | `Systems/Prompt-Library/Competitive-Programming/cp-better-approach-suggestion.md` |
| Initial pattern ID before writing any code | `Systems/Prompt-Library/Competitive-Programming/cp-problem-pattern-recognition.md` |
| Pre-submit bug sweep | `Systems/Prompt-Library/Competitive-Programming/cp-implementation-checklist.md` |

## Discipline rules for using AI in this loop

1. **Struggle first.** Give yourself a real, timed attempt (proportional
   to the problem's difficulty) before asking for any hint at all.
2. **Take the smallest hint offered.** If a hint prompt gives you a
   nudge, go back and try again before asking for the next level of hint
   — don't chain straight to the answer.
3. **Never paste an editorial into an AI to "explain it better."** If
   you've reached for the editorial, the learning move is to log it in
   the Problem Journal as "solved after seeing editorial" and move to
   post-solution analysis — not to outsource understanding it.
4. **Log every AI interaction's outcome** in your Problem Journal entry
   for that problem: what hint level you needed, honestly.

---

## Best practices
- If you notice yourself asking an AI the same kind of question every
  session (e.g. "what pattern is this"), that's a signal for the
  Weakness Finder pass — it may indicate a fluency gap worth deliberate
  practice, not just repeated AI use.
- Recalibrate hint-seeking threshold upward as you improve — a nudge you
  needed at rating 1200 should come from you unaided by rating 1600.

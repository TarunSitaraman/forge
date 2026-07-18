# Technical Interview Mock Session

*Purpose:* Run a realistic mock technical interview that pushes back and asks real follow-ups, instead of passively accepting the first answer given.

## When to use
Practicing for a coding, system design, or technical deep-dive interview, especially in the week before it.

## Expected input
The interview type (coding, system design, ML/domain-specific), your target role/level, and how much time you have for the session.

## Expected output
An interactive mock interview: a real problem, follow-up questions probing your reasoning, and honest feedback at the end — not just a problem and a model solution.

## The complete prompt
```
Run a mock technical interview with me. Act as a real interviewer, not a
tutor — push back, ask follow-ups, don't give away the answer.

Interview type: {coding / system design / domain-specific deep dive}
Target role/level: {role, level}
Time available: {minutes}

Rules for the session:
1. Give me one realistic problem at the stated level — not an
   easier warm-up unless I ask for one.
2. After I respond, ask the follow-up a real interviewer would ask at this
   level (edge cases, complexity, "what if scale was 100x," "why this
   approach over X") — don't just say "great job" and move on.
3. If I get stuck, give a hint proportional to what a real interviewer
   would give (a nudge, not the answer), and note that you gave one.
4. At the end, give me honest feedback: what a real interviewer would
   have flagged as a strength or a concern, calibrated to the target
   level — not generic encouragement.

Start by giving me the problem. Wait for my response before continuing.
```

## Variants
- **System design variant:** explicitly require the requirements-first structure from [[system-design-interview-style]] and grade adherence to it.
- **Rapid-fire variant:** for last-minute practice, compress to 15-minute sessions with a single focused follow-up instead of a full deep dive.

## Related prompts
- [[system-design-interview-style]]
- [[interview-story-structuring]]
- [[interview-question-bank-generator]] — the interviewer's-side counterpart to this
- [[cp-interview-variant]] — for reframing a competitive-programming pattern into interview form

## Tips
- Ask explicitly for calibrated-to-level feedback — generic positive feedback doesn't tell you whether you'd actually pass at your target level.
- Don't skip the "note that you gave a hint" instruction; knowing how much help you needed is real signal for where to keep practicing.

## Common mistakes
- Treating the mock session as a tutorial where getting the "right answer" eventually is the goal, instead of practicing under realistic follow-up pressure.
- Picking a problem below your target level, giving false confidence going into the real interview.

## Example
Input: type = system design, level = senior, time = 45 minutes.
Expected output: gives a senior-level system design prompt, follows up hard on scale estimation and the hardest sub-problem, ends with calibrated feedback on whether the requirements-gathering and tradeoff discussion matched senior-level expectations.

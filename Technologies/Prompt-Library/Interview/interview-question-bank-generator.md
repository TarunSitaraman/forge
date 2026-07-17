# Interview Question Bank Generator (For Interviewers)

*Purpose:* Generate a set of interview questions that actually discriminate between strong and weak candidates for a specific role, instead of generic questions everyone has memorized answers for.

## When to use
Preparing to interview candidates for a role you're hiring for, especially if you're new to interviewing or the role is new.

## Expected input
The role, the specific skills/competencies that actually matter for success in it, and the interview format/time available.

## Expected output
A question bank organized by competency, each with what a strong vs. weak answer looks like — not just a list of questions.

## The complete prompt
```
Generate an interview question bank for this role that actually
discriminates between strong and weak candidates.

Role: {role, level}
Competencies that actually matter for success (from real job needs, not a
generic job description): {competencies}
Format/time: {e.g. "45-minute technical screen"}

For each competency, give 1-2 questions that:
1. Cannot be answered well from memorized "interview prep" content —
   prefer questions requiring reasoning about a specific scenario over
   textbook definitions.
2. Have a clear differentiation between a strong and weak answer — state
   both explicitly, with what specifically separates them (not just
   "strong answers are more detailed").
3. Include a natural follow-up question to probe past a rehearsed-sounding
   initial answer.

Flag any competency in my list that's hard to assess in this interview
format/time, and suggest whether it belongs in a different interview
stage instead.
```

## Variants
- **Take-home/practical variant:** replace questions with a small scoped exercise plus the evaluation rubric.
- **Panel calibration variant:** add a shared rubric section so multiple interviewers score consistently.

## Related prompts
- [[technical-interview-mock-session]]

## Tips
- Ground competencies in what actually matters for the role's real day-to-day, not a generic job description template.
- Insist on the strong-vs-weak answer distinction being specific; vague distinctions lead to inconsistent scoring across interviewers.

## Common mistakes
- Reusing generic, widely-circulated interview questions that candidates have memorized model answers for.
- Assessing a competency (e.g. "collaborates well") in a format that structurally can't reveal it (a solo coding screen), instead of moving it to a better-suited stage.

## Example
Input: role = senior backend engineer, competency = "makes good tradeoffs under ambiguity."
Expected output: a scenario-based question ("here's a partial spec, what would you need to know before designing this, and what would you assume if you couldn't ask") with explicit strong/weak answer distinction based on whether they surface real tradeoffs vs. pick a default silently.

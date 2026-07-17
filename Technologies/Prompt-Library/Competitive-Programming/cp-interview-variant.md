# CP → Technical Interview Variant

*Purpose:* Reframe a competitive programming problem/pattern into the
shape a technical interview would actually present it in — since
interview problems test the same patterns but with different framing,
constraints, and expected communication style.

## When to use
After mastering a CP pattern, when preparing specifically for coding
interviews rather than contests.

## Expected input
The CP problem/pattern you know well, and the target interview context
(company type, seniority level if known).

## Expected output
An interview-style reframing of the same core pattern — realistic
prompt, realistic constraints (usually smaller/vaguer than CP), and the
communication expectations that differ from a contest setting.

## The complete prompt
```
Reframe this CP pattern into how a technical interview would actually
present it — don't just relabel the same problem, adapt the framing and
expectations to the interview context.

CP problem/pattern I know: {problem/pattern}
Interview context: {company type/level if known, otherwise general
"mid-level software engineer screen"}

Give me:
1. A realistic interview-style prompt testing the same core insight —
   interview problems are usually more loosely specified (no formal
   constraints given upfront) and expect YOU to ask clarifying questions,
   unlike a CP problem with everything stated.
2. The 2-3 clarifying questions a strong candidate would ask before
   coding, given the deliberately vague framing.
3. What's different about the expected deliverable: interview solutions
   value clear communication of approach and tradeoffs as much as a
   correct/optimal solution — note where a CP-optimal approach might be
   overkill or where explaining a simpler approach first, then optimizing,
   would score better than jumping straight to the optimal one.
4. The follow-up question an interviewer would likely ask after a
   working solution (scale up, change a constraint, ask for a variant).

Do not give the solution — frame the problem and expectations, then let
me attempt it as I would in a real interview.
```

## Variants
- **System-design-adjacent variant:** for CP patterns that map to real system components (e.g. LRU cache, rate limiter), explicitly bridge to the system design framing too.
- **Whiteboard/no-IDE variant:** add explicit constraint that no compiler/autocomplete is available, changing what's realistic to attempt.

## Related prompts
- [[cp-problem-pattern-recognition]]
- [[technical-interview-mock-session]]

## Tips
- Practice asking the clarifying questions out loud, not just reading them — the interview skill is asking them yourself under a vague prompt, not recognizing them in a list.
- Pay attention to the "simpler first, then optimize" note — over-indexing on CP instincts (jump straight to optimal) can read as poor communication in an interview setting.

## Common mistakes
- Treating the interview variant as pattern-identical to the CP version and skipping the clarifying-question practice, which is often the actual differentiator in interview performance.
- Jumping straight to the most optimal solution without narrating a simpler baseline first, missing the communication signal interviewers look for.

## Example
Input: CP pattern = sliding window for a subarray sum problem.
Expected output: reframes as "find the shortest subarray with at least a given sum, given an unsorted list of positive and negative integers" (deliberately ambiguous on sign), lists clarifying questions about whether negatives are allowed, and notes that starting with a brute force explanation before optimizing to sliding window is expected.

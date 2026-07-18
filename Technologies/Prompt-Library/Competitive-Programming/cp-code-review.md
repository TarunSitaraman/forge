# CP Code Review (Post-Accept)

*Purpose:* Review a solution that already passed for code quality,
robustness, and clarity of reasoning — separate from correctness, which
the judge already confirmed — so you build habits that transfer to real
engineering, not just "whatever passes."

## When to use
After a solution is Accepted, when you want to review HOW it was written,
not whether it works.

## Expected input
The accepted code, and the approach it implements.

## Expected output
Feedback on code clarity, structure, and any accidental fragility (works
on judge's tests but relies on unstated assumptions) — framed as
questions where possible, not just corrections.

## The complete prompt
```
This code passed — review it for quality, not correctness (that's
already confirmed).

Approach: {brief description}
Code: {code}

Review, asking guiding questions where useful rather than just stating
fixes:
1. Readability — could someone else (or you, in 6 months) follow the
   logic without re-deriving the approach from scratch? Flag any part
   that's clever-but-opaque.
2. Naming — do variable/function names communicate their role, or are
   they placeholder-style (`x`, `tmp`, `arr2`) in a way that would slow
   down debugging under contest pressure?
3. Structure — is the solution one monolithic block, or broken into
   named steps (even in a single-file contest solution, a few named
   helper functions/sections aid both readability and debugging)?
4. Accidental fragility — does this rely on an assumption not guaranteed
   by the problem (e.g. input always sorted, no duplicates) that happened
   to hold on the test data but isn't actually guaranteed? Point out the
   specific assumption if so, as a question: "is this guaranteed by the
   constraints, or did it just happen to hold here?"
5. One thing done well — genuinely note it, don't just list criticisms.

Keep this proportional to a contest-code context — don't demand
production-grade abstraction for a one-off script, but do flag anything
that would slow down your own debugging under time pressure.
```

## Variants
- **Team/practice-group variant:** frame for a peer review exchange — add explicit "what would confuse a teammate reading this cold" framing.
- **Language-idiom variant:** add a check for whether the code uses the language's standard library idiomatically vs. reinventing something already built in.

## Related prompts
- [[cp-implementation-checklist]]
- [[cp-better-approach-suggestion]]

## Tips
- Run this on solutions you're proud of, not just messy ones — good code habits compound, and reviewing your best work still surfaces small improvements.
- Take the "accidental fragility" check seriously even on Accepted code — it's exactly the gap that causes a correct-approach solution to fail on a different problem's edge cases.

## Common mistakes
- Skipping this because "it passed, so it's fine" — correctness on given tests and code quality are genuinely different axes worth separate attention.
- Over-applying production-code standards (extensive error handling, full documentation) to contest code where that effort isn't proportional.

## Example
Input: accepted code that assumes an input array is already sorted, though the problem doesn't guarantee this and it happened to be sorted in the test cases.
Expected output: flags this as accidental fragility with the specific question "is sorted input guaranteed by the constraints, or did this test case just happen to be sorted?"

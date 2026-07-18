# Portfolio Narrative Review

*Purpose:* Review a portfolio project write-up for whether it actually
convinces a reviewer of your competence in the 1-2 minutes they'll
realistically spend on it — not whether the project itself is technically
impressive (that's a separate question).

## When to use
Before publishing a new portfolio entry, or when an existing one isn't
generating interest/questions in interviews.

## Expected input
The project write-up (use `../Templates/portfolio-project.md`
structure if not already), and who the target reviewer is (recruiter,
hiring manager, technical interviewer).

## Expected output
A review of whether the write-up conveys real competence and judgment
quickly, with specific rewrites for weak sections.

## The complete prompt
```
Review this portfolio project write-up for reviewer impact, not
technical impressiveness — assume the underlying work is solid; the
question is whether the WRITE-UP conveys that fast enough.

Write-up: {text}
Target reviewer: {recruiter skimming / hiring manager evaluating fit /
technical interviewer prepping follow-up questions}

Review:
1. In the first two sentences, can the reviewer tell what this does and
   why it matters? If not, that's the highest-priority fix.
2. Is YOUR specific contribution clear, especially if this was a team
   project — or does it read as "we built X" without attributing your
   part?
3. Does the "key technical decisions" section (or equivalent) show real
   judgment — a genuine tradeoff navigated — or does it just list
   features built?
4. Is there anything that would raise more questions than it answers in
   a live interview (a claim without evidence, a vague "optimized
   performance" with no specifics)?
5. Is it honest about limitations, in a way that reads as credible
   self-awareness rather than undermining the work?

For each weak section, give the specific rewrite, not just the critique.
```

## Variants
- **Recruiter-first variant:** weight the "first two sentences" check most heavily, since recruiters spend the least time per entry.
- **Interview-prep variant:** weight the "what would raise follow-up questions" check most heavily, to pre-empt live interview gaps.

## Related prompts
- [[resume-bullet-impact-rewrite]]
- [[pr-review-senior-engineer]]

## Tips
- Specify the actual target reviewer — the bar for "convincing" differs meaningfully between a recruiter skim and a technical deep-dive.
- Take the "would raise more questions than it answers" check seriously; it's the section most likely to catch a claim you can't actually defend live.

## Common mistakes
- Writing the portfolio entry to be technically exhaustive rather than reviewer-convincing — length and impressiveness of the underlying project don't automatically translate to a compelling write-up.
- Vague performance/impact claims ("optimized the system") without a number or specific mechanism, which invites skepticism rather than credibility.

## Example
Input: a write-up that says "improved performance significantly" with no further detail.
Expected output: flags this as a credibility gap, rewrites to require the specific number and mechanism (e.g. "reduced p99 latency from 800ms to 120ms by adding a caching layer") or flags it for you to supply the real number.

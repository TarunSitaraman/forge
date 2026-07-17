# Promotion Case Builder

*Purpose:* Build an evidence-based promotion case mapped to the actual next-level expectations at your company, instead of a list of accomplishments with no scope framing.

## When to use
Preparing a promotion packet/self-assessment, several months before the actual review cycle (not the week before).

## Expected input
The next level's stated expectations/rubric (if your company has one), your accomplishments over the review period, and any feedback you've received.

## Expected output
A promotion case mapped explicitly to each expectation, with evidence, and a flagged gap list of expectations not yet clearly met.

## The complete prompt
```
Build my promotion case against these next-level expectations.

Next-level expectations/rubric: {paste the actual rubric if you have one;
otherwise state the informal expectations you've heard}
My accomplishments this period: {list, with any available numbers/scope}
Feedback received: {peer/manager feedback, especially anything about gaps}

For each expectation in the rubric:
1. Match it to the strongest piece of evidence from my accomplishments —
   be specific (which project, what scope, what outcome), not generic
   ("demonstrates leadership").
2. If no accomplishment clearly evidences this expectation, say so
   explicitly — don't stretch a weak example to fit.
3. For expectations with a gap, suggest a specific action I could take in
   the remaining time before review to generate real evidence (not just
   "highlight it more").

Output: expectation-by-expectation mapping with evidence or gap, then a
prioritized list of the top 2-3 gaps to close before the review cycle.
```

## Variants
- **No-formal-rubric variant:** first infer likely next-level expectations from the company's leveling philosophy/role description, then proceed.
- **Manager-advocacy variant:** reframe output as talking points for your manager to use when presenting your case to a promotion committee.

## Related prompts
- [[resume-bullet-impact-rewrite]]
- [[career-plan-prompt]]

## Tips
- Use the real rubric if one exists — vague "expectations" produce a vague case that's easy for a committee to discount.
- Don't let a weak example get stretched to cover a real gap; an honest gap list with time to act on it is more valuable than a padded case.

## Common mistakes
- Building the case the week before the review cycle, leaving no time to generate evidence for genuine gaps.
- Listing accomplishments without mapping them explicitly to the level's actual expectations, leaving the reviewer to do that inference themselves.

## Example
Input: next-level expectation = "influences technical direction beyond own team," accomplishments = mostly within-team project delivery.
Expected output: flags this as a gap, suggests a specific action (e.g. propose and drive a cross-team architecture RFC) with enough runway before the review.

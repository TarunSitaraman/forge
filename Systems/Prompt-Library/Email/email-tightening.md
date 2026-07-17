# Email Tightening for Busy Recipients

*Purpose:* Rewrite an email so the recipient can act on it in 10 seconds — ask up front, context below, no scroll required to know what's needed from them.

## When to use
Before sending any email that requests a decision, action, or approval from someone busy.

## Expected input
The draft email and what you actually need the recipient to do (decide, approve, provide, schedule).

## Expected output
A rewritten email with the ask in the first line, structured so skimming alone is enough to act.

## The complete prompt
```
Rewrite this email so the ask is unmissable and actionable in 10 seconds.

What I need from the recipient: {specific action/decision/approval}
Draft:
{draft}

Rules:
1. First line: the ask, stated as a specific action ("Approve X by
   Friday," not "wanted to touch base about X").
2. If a decision is needed, state the options explicitly and your
   recommendation — don't make the recipient extract the decision from
   narrative.
3. Context goes below the ask, not before it — recipient shouldn't need
   to read to the bottom to know what's needed.
4. Cut anything that doesn't serve the ask or the context needed to make
   it. Move nice-to-know details to a P.S. or drop them.
5. If a deadline exists, state it explicitly in the first line, not
   buried in a later paragraph.

Output the rewritten email only.
```

## Variants
- **Cold outreach variant:** replace "the ask" framing with a one-line value proposition first, ask second.
- **Escalation variant:** add explicit "what happens if no response by X" consequence line.

## Related prompts
- [[dense-prose-tightening]]

## Tips
- State your actual recommendation when asking for a decision — "thoughts?" emails get deprioritized; "I recommend X, let me know if you disagree" gets answered.
- Always put the deadline in the first line if one exists — buried deadlines get missed.

## Common mistakes
- Leading with context/backstory and burying the ask three paragraphs in, so busy recipients skim past it.
- Asking an open-ended question ("what do you think?") when a specific recommendation with a yes/no would get a faster response.

## Example
Input: a long email narrating a project's background before asking, buried at the end, "let me know if this budget works."
Expected output: opens with "Need your approval on a $15K budget increase by Thursday — details below," followed by the condensed context.

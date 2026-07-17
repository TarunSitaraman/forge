# Cold Email Crafting

*Purpose:* Draft a cold email (informational request, referral ask,
business outreach) with a single specific ask and enough genuine
personalization to survive a skeptical, busy inbox skim.

## When to use
Before sending a cold email to someone you don't know, using
`Career/cold-email-templates.md` as a starting structure.

## Expected input
The recipient and why you're reaching out to them specifically, the
exact ask, and the template type (informational / referral / follow-up)
if using one as a base.

## Expected output
A drafted cold email, tight and specific, with a flag on anything too
generic to personalize convincingly.

## The complete prompt
```
Draft a cold email using this context — must read as written specifically
for this recipient, not a template with blanks filled in.

Recipient: {name, role, specific detail about them relevant to why I'm
reaching out}
Why them specifically: {real reason}
The exact ask: {one specific thing — an answer to a question, a referral,
a short call}
Template type (if any): {informational / referral / follow-up — see
../../../Career/cold-email-templates.md}

Rules:
1. Subject line: specific to the ask, not generic ("Quick question" is
   weak; "Quick question about your team's approach to X" is strong).
2. One specific, genuine opening line tied to the recipient — not a
   generic compliment.
3. State who you are in one line, then the ask — don't bury the ask in
   paragraph three.
4. One clear ask only. If I gave you multiple things I want, tell me
   which one to lead with and suggest saving the rest for a follow-up.
5. Explicit low-pressure close ("no worries if you don't have time").
6. Keep total length under 150 words — cold emails get skimmed, not read.

Output the email, then flag any part of my context that's too generic
to personalize convincingly — I'll find something more specific first.
```

## Variants
- **Business/sales-adjacent variant:** add explicit value-to-them framing before the ask, since the calculus differs from a personal networking request.
- **Referral-specific variant:** explicitly acknowledge the awkwardness of the ask and make declining maximally easy, given referral requests carry real social cost for the recipient.

## Related prompts
- [[networking-message-crafting]]

## Tips
- Keep it under 150 words — the tips folder in `Career/cold-email-templates.md` exists precisely because longer cold emails get skimmed and dropped.
- Lead with the ask early; burying it costs response rate more than almost any other single factor.

## Common mistakes
- Stacking multiple asks in one email ("would love advice, and also wondering if you could refer me, and also...") which reduces the odds of any single ask being acted on.
- Writing a generic opening line that could apply to any senior person in the field, undermining the personalization that drives response rate.

## Example
Input: recipient = an engineer at a target company, ask = "referral for a role I just applied to."
Expected output: a sub-150-word email acknowledging the referral ask directly, offering to send a resume/summary either way, explicit "no pressure" close.

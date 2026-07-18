# Difficult Conversation Email

*Purpose:* Draft an email delivering unwelcome news (scope cut, missed deadline, disagreement) that is direct and honest without being needlessly harsh or passive-aggressively vague.

## When to use
Delivering bad news, pushing back on a request, or addressing a conflict in writing rather than a real-time conversation.

## Expected input
The situation, what actually needs to be communicated, the relationship context (peer, manager, report, client), and what outcome you want from the email.

## Expected output
A draft that states the unwelcome fact plainly, gives the reason briefly, and moves to what happens next — without over-apologizing or softening the fact itself into ambiguity.

## The complete prompt
```
Draft this difficult email. Be direct without being harsh.

Situation: {what happened}
What must be communicated: {the actual unwelcome fact — a deadline miss,
a scope cut, a disagreement}
Relationship: {peer / manager / report / client}
Desired outcome: {what you want to happen after they read this}

Rules:
1. State the unwelcome fact in the first two sentences, plainly — no
   burying it under three paragraphs of preamble, and no softening it
   into vagueness that leaves the reader unsure what actually happened.
2. Give the reason in one or two sentences — enough for legitimacy,
   not a defensive essay justifying it.
3. State what happens next / what you're proposing, concretely.
4. Match tone to the relationship: more formal/deferential for a client
   or manager, more direct for a peer — but the fact itself doesn't get
   softer regardless of relationship.
5. Do not over-apologize (more than one genuine acknowledgment reads as
   defensive, not sincere).

Output the draft, then flag anything in the situation you gave me that
seems like it needs to be verified before sending (e.g. an assumption
about why something happened).
```

## Variants
- **Upward variant (to manager):** add an explicit "here's what I need from you" ask if applicable.
- **Client-facing variant:** add an explicit remediation/next-steps commitment with a date.

## Related prompts
- [[email-tightening]]

## Tips
- State the desired outcome explicitly — it shapes whether the email should end with an ask, an FYI, or a commitment.
- Watch for the model over-softening the unwelcome fact into ambiguity; the fact itself should stay clear regardless of tone.

## Common mistakes
- Burying the actual bad news so deep in polite context that the reader doesn't realize what happened until a re-read.
- Over-apologizing to the point that it reads as defensive or undermines confidence in the proposed next steps.

## Example
Input: situation = "we will miss the launch deadline by 2 weeks due to a vendor delay," relationship = client, desired outcome = "they accept the new date without escalating."
Expected output: opens with the new date and the reason in two sentences, states the concrete revised plan, one sincere acknowledgment, no over-apology.

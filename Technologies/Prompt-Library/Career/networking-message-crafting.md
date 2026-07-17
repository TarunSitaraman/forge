# Networking Message Crafting

*Purpose:* Draft a networking outreach message specific enough to the
recipient that it doesn't read as a mail-merge — the single biggest
driver of response rate.

## When to use
Before sending any outreach to someone you don't know well — an
informational interview request, a follow-up from an event, a
reconnection with a dormant contact.

## Expected input
Who you're reaching out to (real, specific details about them — not just
their title), why you specifically want to connect with THEM, and what
you're hoping for (advice, an intro, just staying in touch).

## Expected output
A short, specific message with one clear, low-pressure ask, tailored
enough that it couldn't be sent to anyone else unchanged.

## The complete prompt
```
Draft a networking message to this specific person — it must not read as
something that could be sent to anyone in their role unchanged.

Recipient: {name, role, and something SPECIFIC about them — a project,
a talk, a career path, a mutual connection}
Why I want to connect with them specifically: {real reason}
What I'm hoping for: {advice / intro / staying in touch / referral}
Context of how we're connected (if any): {mutual contact, met at an
event, cold}

Rules:
1. Open with the specific, genuine reason for reaching out to THEM — not
   a generic compliment ("I found your profile impressive").
2. State one clear, small ask — not multiple asks, and not vague ("pick
   your brain sometime").
3. Make it easy to decline ("no worries if you don't have time") —
   this increases response rate by lowering the cost of replying.
4. Keep it short — 4-6 sentences total. A long cold message reads as
   asking for a lot of the recipient's time before they've agreed to
   give any.

Output the message, then flag if anything I gave you feels too generic
to actually personalize — I'll go find something more specific before
sending.
```

## Variants
- **Reconnection variant:** open by acknowledging the time gap honestly rather than pretending regular contact.
- **Post-event variant:** reference the specific conversation/talk content, not just "great meeting you."

## Related prompts
- [[cold-email-crafting]]

## Tips
- Give the model something genuinely specific about the recipient — a message built on generic details will read as generic no matter how it's worded.
- Keep the ask singular; a message with a small ask gets a faster, easier yes than one bundling several requests.

## Common mistakes
- Sending the same phrasing to multiple people with only the name swapped — recipients compare notes more often than senders expect.
- Asking for a big time commitment ("30 minutes to discuss my career") from a first message before any relationship exists.

## Example
Input: recipient = someone who gave a conference talk on a specific migration approach, ask = "would value 15 minutes on how they approached it."
Expected output: opens referencing the specific talk content, asks the specific 15-minute question, includes the explicit "no worries if not" line.

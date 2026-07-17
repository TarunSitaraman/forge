# Dense Prose Tightening Pass

*Purpose:* Cut a draft down to its actual content — removing hedging, filler, and redundant framing — without softening or distorting the claims.

## When to use
After a first draft of any substantial written piece (report, doc, proposal), before it goes to a reader whose time is scarce.

## Expected input
The draft, and the one thing it must accomplish for the reader (decide something, understand something, act on something).

## Expected output
A tightened version plus a list of what was cut and why it was safe to cut.

## The complete prompt
```
Tighten this draft. The reader's job after reading it: {what the reader
must decide/understand/do}.

Draft:
{draft}

Apply:
1. Remove hedging that doesn't change the claim ("I think," "it seems
   like," "in some ways") unless the uncertainty itself is the point.
2. Remove redundant framing — sentences that restate what a heading or
   the next sentence already says.
3. Collapse any paragraph that could be a single sentence without losing
   information the reader needs.
4. Keep every specific number, name, date, and claim exactly as-is — do
   not soften or generalize a specific claim while tightening.
5. If a section doesn't serve the reader's stated job, flag it as a
   candidate for removal rather than silently deleting it.

Output the tightened draft, then a short list: what was cut, and confirm
nothing substantive was lost.
```

## Variants
- **Executive summary variant:** apply this pass specifically to produce a 3-sentence summary preceding the full draft.
- **Technical spec variant:** be more conservative about cutting — precision often requires the "redundant-seeming" restatement.

## Related prompts
- [[email-tightening]]
- [[presentation-narrative-arc]]

## Tips
- State the reader's actual job explicitly — tightening without it risks cutting content the reader actually needed.
- Ask for confirmation that no substantive claim was lost; it catches over-aggressive cuts.

## Common mistakes
- Cutting hedging that was actually load-bearing uncertainty (e.g. "we estimate" before an unverified number) — turning an estimate into a false-confidence claim.
- Tightening a technical spec as aggressively as a prose piece, losing precision that redundancy was protecting.

## Example
Input: a 600-word status update full of "I think," "just wanted to flag," and repeated framing sentences, reader's job = "decide whether to approve budget increase."
Expected output: a ~250-word version stating the ask, the number, and the reason, with all decision-relevant specifics intact.

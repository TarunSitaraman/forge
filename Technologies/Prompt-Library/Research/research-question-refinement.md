# Research Question Refinement

*Purpose:* Turn a vague research interest into a specific, answerable research question before investing time gathering sources.

## When to use
At the very start of a research project, when the topic feels interesting but the actual question isn't yet crisp.

## Expected input
The vague topic/interest, why it matters to you, and any constraints (time available, access to data/sources, required output form — paper, report, decision).

## Expected output
2-3 candidate refined research questions, each scoped to be answerable within the stated constraints, with the tradeoffs of each.

## The complete prompt
```
Refine this into an answerable research question.

Vague topic/interest: {topic}
Why it matters: {motivation}
Constraints: {time available, data/source access, required output}

For 2-3 candidate refined questions:
1. State the question precisely — specific enough that you could describe
   what evidence would answer it, not so broad it invites a book-length
   answer.
2. State what evidence/sources would be needed to answer it, and whether
   that's actually accessible given the constraints.
3. State the scope tradeoff: what does narrowing to this specific question
   sacrifice from the original broad interest?
4. Flag if the original topic is actually 2+ separate questions being
   conflated — this is the most common reason a topic feels
   unmanageably broad.

Recommend one as the best starting point given the constraints, with the
option to expand later.
```

## Variants
- **Thesis-scale variant:** add explicit novelty check — what's already been answered by prior work vs. genuinely open.
- **Quick-decision variant:** for research meant to inform an immediate decision (not publication), bias toward the question most directly actionable.

## Related prompts
- [[literature-review-synthesis]]
- [[paper-critical-reading]]

## Tips
- State real constraints up front — a question refined without them often turns out to be unanswerable given your actual time/access.
- Watch for conflated questions; splitting them is often the single most useful output of this prompt.

## Common mistakes
- Accepting the broadest refined question because it feels most impactful, without checking if it's answerable in the time available.
- Skipping the "what does narrowing sacrifice" step, leading to scope creep back to the original vague topic mid-research.

## Example
Input: topic = "how does remote work affect productivity," constraint = "2 weeks, no access to proprietary company data."
Expected output: proposes narrowing to "what does published research show about remote work's effect on measurable output in software engineering roles specifically," flagging that "productivity" broadly is actually several conflated questions.

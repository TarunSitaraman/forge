# Literature Review Synthesis

*Purpose:* Turn a stack of papers/sources into a synthesized map of the field's actual disagreements and open questions, not a summary-of-summaries.

## When to use
Starting research on a new topic, or writing the related-work section of a paper/report.

## Expected input
A list of sources (papers, articles) with their key claims/findings, and the specific research question you're trying to answer.

## Expected output
A synthesis organized by point of disagreement or open question, not by source — with a clear statement of what's actually settled vs. contested.

## The complete prompt
```
Synthesize these sources into a research map for my question: {research question}

Sources: {list of sources with their key claims/methods/findings}

Do NOT summarize source-by-source. Instead:
1. Group findings by the claim they support or contest — organize around
   the actual points of agreement and disagreement in the field.
2. For each point of disagreement: state each side's position, the
   methodological difference that likely explains the disagreement (e.g.
   different datasets, different definitions of the key term), and which
   side has stronger evidence given the methods used.
3. Identify what's genuinely settled (multiple independent sources agree,
   no serious methodological objections) vs. contested vs. simply
   under-studied.
4. State the specific gap most relevant to my research question — what
   hasn't been answered by any of these sources.
5. Flag any source making a claim its own methodology doesn't support
   (overclaiming).
```

## Variants
- **Systematic review variant:** add explicit inclusion/exclusion criteria and PRISMA-style source flow accounting.
- **Fast-scan variant:** for a quick orientation pass, drop the methodological-disagreement analysis and just map claims to gap.

## Related prompts
- [[paper-critical-reading]]
- [[research-question-refinement]]

## Tips
- Feed in actual claims and methods, not just titles/abstracts — disagreement analysis needs the methodological detail.
- Ask explicitly for overclaiming detection; it's the single highest-value catch in a synthesis pass.

## Common mistakes
- Organizing the synthesis source-by-source instead of claim-by-claim, which just reproduces separate summaries.
- Treating a majority of sources agreeing as "settled" without checking whether they share the same methodological limitation.

## Example
Input: 6 papers on a treatment's efficacy, 4 showing positive effect with self-reported outcomes, 2 showing no effect with objective measures.
Expected output: flags the disagreement as likely methodological (self-report vs. objective measurement), rates the objective-measure studies as stronger evidence, states this as the key open question.

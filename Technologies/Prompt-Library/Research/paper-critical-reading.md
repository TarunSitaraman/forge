# Critical Reading of a Research Paper

*Purpose:* Extract a paper's actual contribution and its real limitations quickly, instead of a passive summary that repeats the abstract.

## When to use
Reading any paper you need to actually evaluate — for a literature review, to decide whether to apply its method, or to cite it responsibly.

## Expected input
The paper (or its key sections: abstract, method, results, limitations), and why you're reading it (what you need to decide or learn).

## Expected output
The real contribution stated in one sentence, the strength of evidence for it, and the specific limitations that matter for your purpose.

## The complete prompt
```
Critically read this paper for my purpose: {why you're reading it — e.g.
"deciding whether to use this method," "writing a related-work section"}

Paper: {text or key sections}

Extract:
1. The actual contribution, in one sentence — what's new here, not what
   the field/problem is (that's background, not contribution).
2. The evidence for the contribution — what was actually measured/proven,
   and does the evidence support the strength of claim made in the
   abstract/conclusion, or does the paper overclaim relative to its own
   results?
3. The specific limitation most relevant to MY purpose — not a generic
   "future work" list, but the limitation that would actually affect
   whether I can use/trust this for what I need.
4. One question this paper leaves unanswered that matters for my purpose.

Be honest if the paper's claims are weaker than its framing suggests —
don't take the abstract's claims at face value without checking the
results section supports them.
```

## Variants
- **Method-reuse variant:** add explicit check of whether the paper's experimental conditions match your intended use case closely enough to trust generalization.
- **Peer-review variant:** add explicit check for reproducibility (is enough detail given to actually replicate).

## Related prompts
- [[literature-review-synthesis]]
- [[research-question-refinement]]

## Tips
- State your actual purpose for reading — the same paper yields different "relevant limitations" depending on whether you're citing it or trying to reuse its method.
- Push back on abstracts; the gap between claimed and actually-evidenced contribution is one of the most common things to miss on a passive read.

## Common mistakes
- Summarizing the paper's background/motivation section as if it were the contribution.
- Accepting the stated limitations section as complete, without checking for limitations the paper doesn't self-report but that matter for your specific purpose.

## Example
Input: a paper claiming a new method "significantly improves" performance, purpose = "deciding whether to adopt this method."
Expected output: extracts the actual contribution, notes the improvement was only tested on one narrow benchmark not resembling your use case, flags this as the limitation that matters most for your adoption decision.

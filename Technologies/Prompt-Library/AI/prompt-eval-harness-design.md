# Prompt / Eval Harness Design

*Purpose:* Design a real evaluation set and metric for an LLM-powered feature before shipping changes to its prompt, instead of eyeballing a few outputs.

## When to use
Before shipping any change to a production prompt, or when a feature has been running on "looks good to me" for a while.

## Expected input
The task the prompt performs, examples of good and bad outputs seen so far, and what "correct" means for this task.

## Expected output
An eval set structure (categories of test cases), the scoring method, and a pass/fail threshold recommendation.

## The complete prompt
```
Design an evaluation harness for this LLM task, so prompt changes can be
measured instead of eyeballed.

Task: {what the prompt does}
Examples of good outputs seen: {examples}
Examples of bad outputs seen: {examples, especially real production failures}
Definition of "correct" for this task: {how you'd judge correctness}

Design:
1. Test case categories — the distinct kinds of input this prompt must
   handle well (happy path, edge cases, adversarial/injection attempts,
   ambiguous input, out-of-scope input it should refuse or flag). Aim for
   real diversity, not 20 variations of the same case.
2. For each category, 2-3 concrete example test cases derived from the
   bad-output examples given, since those are known failure points.
3. Scoring method — can this be scored programmatically (exact match,
   regex, structured field validation) or does it need LLM-as-judge/human
   review? Justify which, per category.
4. Pass threshold — what score change would you consider a regression vs.
   noise, given expected variance in this scoring method?
5. How to run this on every prompt change (manual checklist vs. automated
   in CI) given the team's current tooling maturity.
```

## Variants
- **RAG-specific variant:** add retrieval-precision/recall as a separate scored dimension from generation quality.
- **Regression-guard variant:** frame explicitly around "does this change break any previously-fixed bad case."

## Related prompts
- [[llm-feature-feasibility-check]]
- [[rag-architecture-review]]

## Tips
- Seed the eval set from real production failures, not hypothetical edge cases — those are the ones that actually recur.
- Prefer programmatic scoring wherever the output has any structure; reserve LLM-as-judge for genuinely open-ended generation.

## Common mistakes
- Building an eval set of only happy-path cases, which never catches the regressions that matter.
- Re-running the same few manual examples after every change instead of a structured, repeatable set.

## Example
Input: task = "extract structured order details from customer emails," known bad outputs = missing quantity field on multi-item orders.
Expected output: eval categories include multi-item orders as their own category with 3 concrete test cases, scored via structured field match, run before every prompt deploy.

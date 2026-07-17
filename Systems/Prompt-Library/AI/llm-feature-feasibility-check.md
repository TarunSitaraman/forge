# LLM Feature Feasibility Check

*Purpose:* Decide, before building, whether an LLM is actually the right tool for a proposed feature — and if so, what failure modes to design around — instead of discovering it in production.

## When to use
When a feature proposal says "use AI to do X," before any implementation starts.

## Expected input
The proposed feature, the task the LLM would perform, and the cost of a wrong/bad output in this context.

## Expected output
A go/no-go recommendation, the specific failure modes to design against, and the evaluation approach to validate it before launch.

## The complete prompt
```
Assess whether an LLM is the right approach for this feature, before we build it.

Feature: {description}
What the LLM would do: {specific task — classify, generate, extract, summarize, converse}
Cost of a wrong output: {e.g. "annoying" vs. "financial/legal/safety impact"}
Volume: {expected requests/day}
Latency/cost budget: {constraints}

Assess:
1. Is this task actually a good fit for an LLM (fuzzy, language-shaped,
   tolerant of some error) or would a deterministic/rules-based/classical
   ML approach be more reliable and cheaper? Be honest even if the answer
   is "don't use an LLM here."
2. Given the cost-of-wrong-output, what confidence/verification layer is
   needed (human review, structured output validation, confidence
   threshold, fallback to deterministic path)?
3. Name the 3 most likely failure modes specific to this task (e.g.
   hallucinated facts, prompt injection via user input, inconsistent
   output format) and the mitigation for each.
4. What would you measure in an eval before launch to know this is safe
   to ship (not just "it looks good in a demo")?

Give a clear go/no-go, not just tradeoffs.
```

## Variants
- **User-facing chat variant:** add explicit prompt-injection and jailbreak resistance assessment.
- **High-stakes decision variant:** require human-in-the-loop as a non-negotiable design constraint, not just a suggestion.

## Related prompts
- [[prompt-eval-harness-design]]
- [[rag-architecture-review]]
- [[prompt-injection-hardening]]

## Tips
- State the cost of a wrong output honestly — it's the single biggest driver of how much verification the feature needs.
- Don't skip the "is this even a good fit for an LLM" question — the instinct to reach for one is often wrong for structured/deterministic tasks.

## Common mistakes
- Building the feature first and asking about failure modes after a bad output ships to a user.
- Treating "it works in my testing" as sufficient evidence without a real eval set covering edge cases.

## Example
Input: feature = "auto-categorize support tickets," cost of wrong output = "low, just mis-routes to wrong queue, humans catch it."
Expected output: go recommendation, with confidence threshold + fallback-to-manual-triage below it, and a suggested eval set of historically mis-routable tickets.

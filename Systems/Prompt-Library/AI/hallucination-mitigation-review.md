# Hallucination Mitigation Review

*Purpose:* Review an LLM feature's design specifically for hallucination risk and add the minimum verification layer needed, without over-engineering for tasks that don't need it.

## When to use
When a feature involves the LLM stating facts, numbers, citations, or claims that could be wrong and matter if wrong.

## Expected input
What the LLM is claiming/generating, its source of truth (if any — retrieved docs, tool output, or none/parametric knowledge), and the consequence of a false claim.

## Expected output
A specific set of mitigations matched to the actual risk level, not a blanket "add a disclaimer."

## The complete prompt
```
Review this LLM feature for hallucination risk and recommend mitigations
matched to the actual consequence of being wrong.

What it claims/generates: {description}
Source of truth available: {retrieved documents / tool output / none —
parametric knowledge only}
Consequence if wrong: {e.g. "user makes a bad purchase decision" vs.
"minor inconvenience"}
Current mitigations (if any): {existing guardrails}

Assess:
1. Is the LLM grounded in retrieved/tool-verified content for every factual
   claim it makes, or relying on parametric knowledge for anything that
   matters? Flag every ungrounded claim category specifically.
2. Given the consequence-if-wrong, what verification is proportionate:
   citation-to-source display, confidence scoring, a "the model doesn't
   know" refusal path, or human review before the claim reaches the user?
3. Design the refusal/uncertainty behavior explicitly: what should the
   model say when it doesn't have grounded information, instead of
   guessing plausibly?
4. What's the cheapest mitigation that meaningfully reduces risk here —
   don't recommend a heavyweight verification pipeline for a
   low-consequence feature.
```

## Variants
- **RAG-grounded variant:** add explicit citation-attribution requirement — every claim must trace to a retrieved chunk.
- **Numeric-claims variant:** require any numeric claim (price, date, quantity) to come from a tool call, never generated text.

## Related prompts
- [[rag-architecture-review]]
- [[llm-feature-feasibility-check]]

## Tips
- Separate "would be nice to verify" from "must be verified" using the actual consequence — not every claim needs the same rigor.
- Design the explicit refusal behavior; an LLM without a designed "I don't know" path will guess plausibly instead.

## Common mistakes
- Adding a generic "AI can make mistakes" disclaimer as the only mitigation for a high-consequence claim.
- Grounding retrieval but not requiring the generation step to actually cite it, so hallucination can still slip in on top of good retrieval.

## Example
Input: feature states product prices, sourced sometimes from a live tool call and sometimes from model memory of "typical" prices.
Expected output: flags model-memory pricing as unacceptable given consequence (bad purchase decisions), requires all prices come from the tool call with no fallback to generated numbers.

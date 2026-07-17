# Prompt Injection Hardening Review

*Purpose:* Review an LLM application's handling of untrusted input (user messages, retrieved documents, tool outputs) for prompt injection risk, and add proportionate mitigations.

## When to use
Before shipping any LLM feature that processes untrusted external content — user input, web content, documents, emails, tool/API responses.

## Expected input
Description of the application, what untrusted content flows into the prompt, and what actions the model can take (read-only vs. can it call tools/take actions).

## Expected output
A risk assessment of injection surfaces and specific mitigations matched to the actual capability the model has (higher capability = higher required rigor).

## The complete prompt
```
Review this application for prompt injection risk.

Application: {description}
Untrusted content sources feeding into the prompt: {user input, retrieved
web/doc content, email bodies, tool/API responses, etc. — name each}
Model capabilities: {read-only/generate text only, vs. can call tools,
send messages, modify data, etc.}

Assess:
1. For each untrusted content source, could it plausibly contain text
   designed to redirect the model's behavior (e.g. a document containing
   "ignore previous instructions and instead...")? Rate the plausibility
   given the source (a random webpage: high; a vetted internal doc: lower
   but not zero).
2. Given the model's actual capabilities, what's the worst case if an
   injection succeeds — is it "outputs a wrong answer" (low stakes) or
   "takes an action with real-world effect" (high stakes)? Match rigor to
   this, not to a blanket standard.
3. Recommend specific mitigations: clear delineation of untrusted content
   in the prompt (explicit tags/boundaries), instructions to treat
   content within those boundaries as data not commands, and — for any
   high-stakes action — a confirmation step that can't be bypassed by
   injected text alone.
4. Flag if any untrusted content is currently concatenated into the
   prompt with no delineation from trusted instructions — this is the
   highest-risk pattern and should be fixed regardless of other
   mitigations.

Match the strictness of your recommendations to the actual capability
risk — don't recommend heavyweight mitigation for a read-only, low-stakes
feature.
```

## Variants
- **Agentic/tool-calling variant:** require an explicit allowlist of actions that can never be triggered by content originating from untrusted sources alone.
- **Multi-agent variant:** add explicit review of what one agent's output (which may itself be attacker-influenced) can cause a downstream agent to do.

## Related prompts
- [[hallucination-mitigation-review]]
- [[system-prompt-design-review]]
- [[llm-feature-feasibility-check]]

## Tips
- Match mitigation rigor to actual capability — a read-only summarizer needs far less than an agent that can send emails or spend money.
- Always check for undelineated concatenation of untrusted content into the prompt; it's the single most common and most fixable injection risk.

## Common mistakes
- Treating prompt injection as a solved problem after adding a generic "ignore any instructions in user content" line, without capability-matched safeguards for high-stakes actions.
- Applying the same heavyweight mitigation to every feature regardless of what the model can actually do if manipulated, wasting effort on low-risk paths.

## Example
Input: an agent that reads emails and can also send emails/take calendar actions, emails are the untrusted content source.
Expected output: flags this as high-stakes given the agent's action capability, recommends a hard confirmation step for any send/modify action that cannot be satisfied by email content alone, plus explicit content delineation in the prompt.

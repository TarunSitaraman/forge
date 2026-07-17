# System Prompt Design Review

*Purpose:* Review a system prompt for an LLM application against the failure modes that actually matter in production — conflicting instructions, missing guardrails, and untested edge cases — before it ships.

## When to use
Before shipping a new system prompt for a production LLM feature, or reviewing an existing one that's misbehaving.

## Expected input
The system prompt, the application's purpose, and any known bad behaviors observed so far.

## Expected output
A structured review flagging conflicts, gaps, and untested scenarios, with specific fixes.

## The complete prompt
```
Review this system prompt for a production LLM application.

System prompt: {prompt}
Application purpose: {what this is for, who uses it}
Known bad behaviors observed: {if any — real examples of the model
misbehaving in production}

Check:
1. Internal conflicts — any two instructions in the prompt that could
   contradict each other given certain user inputs (e.g. "always answer
   directly" vs. "always ask clarifying questions first" without a
   stated priority).
2. Missing guardrails — for the application's actual risk profile, is
   there explicit handling for: off-topic requests, requests to ignore
   prior instructions (prompt injection), requests for content outside
   the app's scope?
3. Ambiguous priority — when instructions compete (e.g. brevity vs.
   completeness), is there an explicit tiebreaker, or is the model left
   to guess?
4. Testability — for each of the known bad behaviors, does the prompt
   contain an instruction that directly addresses it, or does the
   behavior indicate a genuine gap?
5. Length/focus — is the prompt trying to do too much (should some of
   this be split into a separate call or a smaller focused prompt)?

For each issue found, give the specific line to add or change.
```

## Variants
- **Multi-turn agent variant:** add explicit check for how the prompt handles conversation history growing long (context management instructions).
- **Tool-using agent variant:** add explicit check for tool-selection guidance and error-handling-on-tool-failure instructions.

## Related prompts
- [[prompt-failure-diagnosis]]
- [[llm-feature-feasibility-check]]

## Tips
- Bring real observed bad behaviors if any exist — reviewing a prompt in the abstract misses the specific gaps that actually bite in production.
- Watch for instructions that sound reasonable individually but conflict under specific inputs; this is the most common source of inconsistent behavior.

## Common mistakes
- Adding more instructions to fix a bad behavior without checking whether it conflicts with an existing instruction, creating a new inconsistency.
- Letting the system prompt grow indefinitely as a catch-all instead of splitting distinct concerns into separate, focused prompts/calls.

## Example
Input: a customer support prompt with "be concise" and "always explain your reasoning in detail" both present with no priority stated.
Expected output: flags the conflict, recommends an explicit tiebreaker (e.g. "be concise by default; explain reasoning only if the user asks why").

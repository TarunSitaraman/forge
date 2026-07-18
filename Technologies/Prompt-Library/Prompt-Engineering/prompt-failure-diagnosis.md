# Prompt Failure Diagnosis

*Purpose:* Diagnose why a prompt is producing bad outputs by isolating whether the failure is in instructions, context, or the task's inherent difficulty for the model — instead of randomly rewording the prompt.

## When to use
When a prompt is producing inconsistent or wrong outputs and the instinct is to just try rewording it.

## Expected input
The prompt, 2-3 examples of bad outputs it produced, and what the correct output should have been for each.

## Expected output
A diagnosis of the specific failure category, with the targeted fix — not a full prompt rewrite when a small fix would do.

## The complete prompt
```
Diagnose why this prompt is failing, using the concrete bad examples given.

Prompt: {prompt}
Bad output examples: for each, give {input}, {output produced}, {what the
output should have been}

Classify each failure into one of:
1. Ambiguous instructions — the prompt doesn't actually specify the
   behavior needed for this case (the model did something reasonable
   given what it was told, but what it was told was underspecified).
2. Missing context — the model needed information it wasn't given
   (an example, a definition, a piece of data) to produce the right
   output.
3. Format/structure failure — the instructions were clear but the model
   didn't follow the requested output structure (fix: add an explicit
   template or example of the exact output format).
4. Task difficulty — the task genuinely requires reasoning beyond what a
   single prompt can reliably elicit (may need decomposition into steps,
   chain-of-thought, or breaking into multiple calls).

For each bad example, name the category and the SPECIFIC line to add or
change in the prompt to fix it — not a full rewrite unless multiple
examples point to the same structural problem.
```

## Variants
- **Few-shot variant:** if diagnosis repeatedly points to ambiguity, recommend adding 1-2 concrete input/output examples directly in the prompt instead of more instructional prose.
- **Agentic/tool-use variant:** add a category for "tool selection failure" — model had the right information but chose the wrong tool/action.

## Related prompts
- [[system-prompt-design-review]]
- [[prompt-eval-harness-design]]

## Tips
- Bring real bad-output examples, not a description of "it doesn't work well" — diagnosis needs the actual input/output pairs.
- Resist rewriting the whole prompt on the first bad example; check if a small, targeted addition (an example, a clarified instruction) fixes it first.

## Common mistakes
- Treating every failure as a prompt-wording problem when it's actually a missing-context or task-difficulty problem that wording can't fix.
- Rewriting the entire prompt after one bad example, potentially breaking cases that were working correctly.

## Example
Input: a summarization prompt that includes irrelevant details in 2 of 3 bad examples.
Expected output: diagnoses ambiguous instructions (no explicit relevance criterion given), recommends adding one sentence defining what counts as relevant, rather than a full rewrite.

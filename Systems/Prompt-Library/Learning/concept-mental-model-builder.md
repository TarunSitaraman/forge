# Concept Mental Model Builder

## Purpose
Build a durable, transferable mental model for a new technical concept —
the one framing that makes the concept's behavior predictable — instead
of a surface-level definition that doesn't generalize past the example
it was learned from.

## When to Use
When learning a new technical concept (not a whole framework — see
`Systems/Playbooks/learning-a-framework.md` for that) that you keep
having to re-look-up because it hasn't "clicked."

## Expected Inputs
The concept, what you already understand that's adjacent to it, and the
specific thing that keeps confusing you about it.

## Expected Outputs
A precise mental-model statement (not just a definition), an analogy
stress-tested for where it breaks down, and a check for whether you can
now predict behavior in a case you haven't seen yet.

## Complete Prompt
```
Help me build a real mental model for this concept — not just a
definition I'll forget, something that lets me predict behavior in cases
I haven't seen yet.

Concept: {what you're trying to learn}
What I already understand that's adjacent: {related concepts you're
comfortable with}
What specifically keeps confusing me: {the actual point of confusion,
not just "I don't get it"}

Process:
1. State the core mental model in one or two sentences — the framing
   that, once internalized, makes the concept's behavior predictable
   rather than memorized case by case.
2. Give ONE analogy to something I already understand, then explicitly
   state where the analogy breaks down — an analogy that's presented as
   perfect will mislead me later.
3. Directly address my specific point of confusion using the mental
   model, not a generic re-explanation.
4. Test my understanding: give me a NEW scenario involving this concept
   that I haven't seen, and ask me to predict what happens before you
   tell me the answer.
5. Only after I respond, confirm or correct my prediction — and if I
   got it wrong, that reveals exactly which part of the mental model
   didn't land, so we can fix that specifically.
```

## Variations
- **Debugging-driven variant:** anchor the concept explanation in a real
  bug you hit, since the mental model that would have prevented it is
  usually the most memorable one.
- **Interview-prep variant:** end with "how would you explain this to an
  interviewer in 30 seconds" as the final compression test.

## Tips
- Insist on the prediction-test step even when it feels like it's slowing you down — it's the actual test of whether the mental model transferred.
- Revisit a concept's mental model after you've used it in a real project; understanding usually deepens on the second pass.

## Common Mistakes
- Accepting a definition-level explanation as understanding, without
  testing it against a novel scenario.
- Trusting an analogy without checking where it breaks down, then being
  confused later when a case falls outside the analogy's validity.
- Skipping the prediction-test step, which is what actually reveals
  whether the mental model transferred or just the specific example did.

## Example Usage
Input: concept = "Python's GIL," confusion = "why does multithreading not
speed up CPU-bound code." Expected output: states the mental model (one
thread holds the GIL at a time for Python bytecode execution), gives an
analogy to a single-lane checkout line, notes where the analogy breaks
down (I/O-bound work releases the GIL, unlike a checkout line), then
tests with a new scenario (a CPU-bound vs. I/O-bound multithreaded
example) before confirming your prediction.

## Related Prompts
- [[learning-plan-generator]]
- [[technical-explainer-for-non-experts]]

# Complexity Cheatsheet

*Guidance: this is a fast decision aid for the "what complexity do I need
to target" step — not an algorithm reference. It intentionally contains
no algorithm explanations; pairing a constraint with a complexity bound
is the whole point, so you form the reflex of deriving the target
complexity from constraints before you start coding (see
`Technologies/Prompt-Library/Competitive-Programming/cp-problem-pattern-recognition.md`).*

---

## Constraint → target complexity (rule of thumb, ~1-2s time limit)

| n up to | Safe target complexity |
|---|---|
| ~10-12 | O(n!) or O(2^n · n) |
| ~20-25 | O(2^n) |
| ~500 | O(n³) |
| ~5,000 | O(n²) |
| ~10^5 - 10^6 | O(n log n) |
| ~10^7 - 10^8 | O(n) |
| ~10^9+ | O(log n) or O(√n) — must avoid iterating over n at all |

*(These are rules of thumb, not guarantees — always sanity-check against
the actual time limit and constant factor of your approach.)*

## Self-check questions before coding

- What's the largest input size given, and what does the table above say
  I need to hit?
- Does my planned approach's complexity actually match that target — have
  I computed it, not just assumed it "should be fine"?
- Is there a hidden extra factor I'm not accounting for (e.g. a sort
  inside a loop, a linear search where a hash lookup was possible)?

## My personal complexity blind spots

*(Fill this in from your own Mistake Tracker — e.g. "I habitually forget
that repeated string concatenation in a loop is O(n²), not O(n)." This
section should be personal and specific, not generic advice.)*

---

## Best practices
- Use this table as the FIRST step on any new problem, before any
  approach is chosen — not as a reference to check after you're already
  stuck.
- Keep the "personal blind spots" section current; it's the part of this
  cheatsheet that actually earns its place in Forge over the generic
  table above.

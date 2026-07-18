# Refactor a Legacy Function Safely

## Purpose
Restructure a poorly-factored but working function into something
maintainable, without changing observed behavior — refactoring under a
safety net, not a rewrite from memory of what it "should" do.

## When to Use
Before adding a feature to a function that's grown unwieldy, or when a
function keeps causing bugs because its structure obscures its logic.

## Expected Inputs
The function's current code, its callers (or a description of how it's
invoked), and any existing tests covering it.

## Expected Outputs
A refactored version with identical external behavior, a list of what
changed and why, and — if no tests existed — the characterization tests
written first to lock in current behavior.

## Complete Prompt
```
Refactor this function for maintainability. Behavior must not change —
treat this as restructuring, not a rewrite.

Function: {code}
Callers / how it's invoked: {description}
Existing tests: {paste, or "none"}

Process:
1. If there are no tests, first write characterization tests that pin
   down the CURRENT behavior (including edge cases and any quirky
   behavior that looks unintentional) — do not "fix" anything yet.
2. Identify the specific structural problems (mixed abstraction levels,
   duplicated logic, unclear naming, deep nesting) — name each one
   before touching code.
3. Refactor incrementally: one structural change at a time, each
   preserving behavior verified by the characterization tests.
4. Do NOT change behavior opportunistically ("while I'm here, I'll also
   fix this bug") — flag any suspected bug separately instead of folding
   it into the refactor.
5. Output the refactored code, the characterization tests (if written),
   and a list of each structural change made and why.
```

## Variations
- **No-test-coverage variant:** treat writing characterization tests as
  the primary deliverable, refactoring only after they exist and pass.
- **Public API variant:** add an explicit check that the function's
  signature/contract stays unchanged for any external caller.

## Tips
- Never skip characterization tests to save time — they're what makes the refactor verifiably safe rather than hopeful.
- Keep each incremental change small enough to review in isolation; a giant refactor diff is as hard to review as the original mess.

## Common Mistakes
- Fixing a suspected bug in the same pass as the refactor, making it
  impossible to tell whether a behavior change was intentional or an
  accidental regression.
- Refactoring without characterization tests first, relying on manual
  inspection to confirm behavior is unchanged.
- Refactoring in one large diff instead of incremental, independently
  verifiable steps.

## Example Usage
Input: a 120-line function mixing validation, business logic, and
logging with no tests. Expected output: characterization tests capturing
current behavior (including an edge case with empty input that returns a
specific default), followed by a refactor splitting validation and
logging into separate functions, with the suspected-but-unconfirmed bug
in the default-handling flagged separately rather than silently fixed.

## Related Prompts
- [[code-simplification-pass]]
- [[pr-review-senior-engineer]]
- [[extract-reusable-abstraction]]

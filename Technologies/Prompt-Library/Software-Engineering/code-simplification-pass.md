# Code Simplification Pass

*Purpose:* Reduce a piece of working code to its essential complexity — remove abstractions, indirection, and defensive code that don't earn their keep — without changing behavior.

## When to use
After a feature works and tests pass, before merging, when the implementation feels heavier than the problem warranted.

## Expected input
The working code (function, module, or file), and a one-line statement of what it must still do after simplification.

## Expected output
A simplified version of the code plus a short list of what was removed and why it was safe to remove.

## The complete prompt
```
You are simplifying working code. Behavior must not change — only reduce
unnecessary complexity.

Code:
{code}

Invariant that must hold after simplification: {invariant}

Apply, in order:
1. Remove abstractions used exactly once (inline them).
2. Remove parameters, flags, or config that are never varied in practice.
3. Remove defensive checks for states that cannot actually occur given the
   callers — but only after you've verified they cannot occur; state that
   verification explicitly.
4. Collapse indirection (wrapper functions, unnecessary interfaces) that
   exist for a "future" that hasn't arrived.
5. Do not remove tests, error handling for real external failure modes, or
   anything the invariant depends on.

Output:
- Simplified code
- Bullet list: what was removed, and the one-line reason it was safe
- Anything you considered removing but kept, with why
```

## Variants
- **Aggressive mode:** also flag entire files/modules as removable if unused.
- **Library code variant:** be more conservative — public APIs have unknown external callers, so keep flags/parameters that external consumers might depend on.

## Related prompts
- [[pr-review-senior-engineer]]
- [[architecture-decision-record]]
- [[refactor-legacy-function]] — for restructuring under a safety net, not just simplifying working code
- [[extract-reusable-abstraction]]

## Tips
- Always state the invariant explicitly — "must not change behavior" is too vague on its own; name the specific contract.
- Run existing tests after simplification, not just diff review.

## Common mistakes
- Simplifying public/library APIs as aggressively as internal code — breaks callers.
- Removing "unused" error handling that actually guards against a real, if rare, external failure.

## Example
Input: a 40-line function with three boolean flags, only one combination of which is ever called in the codebase.
Expected output: a ~15-line function with the flags removed and hardcoded to the one real usage, with a note on which combinations were dropped.

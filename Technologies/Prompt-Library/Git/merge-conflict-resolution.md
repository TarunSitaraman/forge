# Merge Conflict Resolution Review

## Purpose
Resolve a merge/rebase conflict by understanding what BOTH sides were
trying to accomplish, not by mechanically picking one side or the other
— conflict resolution is a logic problem, not a text-diff problem.

## When to Use
When a merge or rebase produces a conflict that isn't a trivial
whitespace/formatting clash — where both sides made a real logical
change to overlapping code.

## Expected Inputs
The conflicting hunk (with both sides' markers), and — critically — what
each side's change was trying to accomplish (from the commit messages or
your own knowledge of the two branches).

## Expected Outputs
A resolution that preserves the INTENT of both changes (not just
syntactically valid merged text), with an explicit flag if the two
changes are actually incompatible and need a human decision.

## Complete Prompt
```
Help me resolve this merge conflict by understanding both sides' intent,
not just merging the text.

Conflicting hunk:
{paste the full conflict markers, <<<<<<< ... ======= ... >>>>>>>}

What "ours"/current branch was trying to do: {description}
What "theirs"/incoming branch was trying to do: {description}

Process:
1. Explain what each side's change actually does, in the context of the
   surrounding code — not just what the diff text shows.
2. Determine whether the two changes are complementary (can be combined
   to satisfy both intents), one supersedes the other (one side's change
   makes the other moot), or genuinely conflicting (satisfying one
   breaks the other's intent).
3. Propose the resolved code for the complementary/superseding cases.
4. If genuinely conflicting: do NOT silently pick one side. State
   explicitly that this needs a human decision, and explain the tradeoff
   each choice implies.
5. After resolving, name what to test/verify to confirm both original
   intents are actually satisfied by the merged result — a
   syntactically clean merge can still be behaviorally wrong.
```

## Variations
- **Long-lived-branch variant:** add explicit attention to whether the
  conflict reveals the branches have diverged so much that a full rebase
  (not just conflict resolution) is the healthier fix.
- **Config/lockfile variant:** for conflicts in generated files
  (lockfiles, build artifacts), prefer regenerating the file over
  manually resolving the conflict markers.

## Tips
- Read both commit messages before touching the conflict markers — intent almost always resolves the conflict faster than staring at the diff.
- When genuinely unsure, escalate to a human rather than guessing; a wrong silent resolution is worse than a delayed merge.

## Common Mistakes
- Resolving a conflict by picking "ours" or "theirs" wholesale without
  understanding what the other side was trying to fix, silently
  reintroducing a bug the other side had already solved.
- Treating a syntactically clean resolution as automatically correct
  without verifying the merged behavior actually satisfies both original
  intents.
- Manually resolving conflicts in generated/lockfiles instead of
  regenerating them from the merged source.

## Example Usage
Input: both branches modified the same validation function — one added a
new required field check, the other refactored the function's structure.
Expected output: identifies these as complementary, applies the new
field check within the refactored structure, and names the specific test
case (submitting without the new field) to verify the merge preserved
both intents.

## Related Prompts
- [[commit-history-cleanup]]
- [[pr-review-senior-engineer]]

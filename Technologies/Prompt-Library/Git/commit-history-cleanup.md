# Commit History Cleanup Before Merge

## Purpose
Turn a messy sequence of local, unpushed commits ("wip," "fix typo,"
"actually fix it") into a clean, reviewable history before opening a PR —
without rewriting anything already shared with others.

## When to Use
Before opening a pull request, when local commit history doesn't reflect
logical, reviewable units of change.

## Expected Inputs
The commit log for the branch (`git log --oneline`), confirmation these
commits are NOT yet pushed/shared, and a sense of the logical changes
that should exist.

## Expected Outputs
A plan for an interactive rebase (squash/reorder/reword) producing
commits that each represent one logical, reviewable change — never
applied to already-shared history.

## Complete Prompt
```
Help me clean up this commit history before opening a PR.

Commit log: {git log --oneline output}
Confirmed NOT pushed/shared: {yes — required before proceeding}
The logical changes this branch should represent: {describe, e.g. "add
the feature, plus one unrelated bug fix I found along the way"}

Process:
1. Group the existing commits into the logical changes they actually
   represent — a "wip" and its later "fix typo" commit belong squashed
   into the same logical change; an unrelated fix belongs as its own
   commit, not folded into the feature.
2. Propose the `git rebase -i` plan: which commits to squash together,
   which to reword, and the final commit message for each resulting
   commit (imperative mood, stating why).
3. Flag if any commit mixes two unrelated concerns that should be split
   apart rather than squashed together — splitting is harder than
   squashing, so call this out explicitly if needed.
4. Remind me this rewrites history — confirm again these commits aren't
   already pushed/shared before giving the final rebase todo list.
```

## Variations
- **Already-pushed-but-solo-branch variant:** if the branch is pushed
  but you're the only one working on it, note that a force-push is safe
  here specifically, but still not for any shared/reviewed branch.
- **Split-commit variant:** for a commit that mixes concerns, walk
  through `git reset` + `git add -p` to split it into separate commits
  before the squash plan.

## Tips
- Triple-check the branch is genuinely unshared before any interactive rebase — this is the one Git operation with real destructive potential for collaborators.
- Write the final commit messages as if a reviewer with zero context will read only the message, not the diff.

## Common Mistakes
- Rewriting history on a branch that's already been pushed and pulled by
  someone else, causing their local history to diverge painfully.
- Squashing an unrelated bug fix into the main feature commit, making it
  invisible to reviewers and impossible to `git revert` independently
  later.
- Writing squashed commit messages that just concatenate the original
  "wip"/"fix" messages instead of describing the actual logical change.

## Example Usage
Input: log shows "wip," "wip," "fix typo," "add tests," "fix unrelated
lint issue." Expected output: squash the three wip/typo commits into one
"Add X feature" commit, keep "add tests" separate or folded in per
preference, keep the unrelated lint fix as its own commit with its own
message.

## Related Prompts
- [[pr-review-senior-engineer]]
- [[merge-conflict-resolution]]

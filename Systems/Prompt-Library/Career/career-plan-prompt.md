# Career Plan Pressure-Test

*Purpose:* Pressure-test a stated career direction against real evidence
from your current situation — catching a plan built on aspiration alone
before a quarter gets spent chasing it.

## When to use
When filling out or reviewing `Systems/Career/career-plan.md`, especially
at the quarterly review point.

## Expected input
The stated direction/target, your current role and honest gap analysis,
and what's changed since the last review (if any).

## Expected output
A pressure-tested assessment of whether the direction and planned actions
are realistic and evidence-based, with specific gaps in the plan flagged.

## The complete prompt
```
Pressure-test this career plan — don't just validate it, find the real gaps.

Current role/situation: {description, what's working, what's not}
Target direction: {specific target role/level, and the stated reason why}
Gap analysis so far: {what's needed vs. what you have}
Planned actions this quarter: {specific actions}
What's changed since last review (if applicable): {updates}

Assess:
1. Is the target direction specific enough to act on, or is it still a
   vague aspiration ("grow my career") dressed up as a plan? If vague,
   push back and ask for the specific version.
2. For the gap analysis: is each gap something the planned actions would
   actually close, or is there a mismatch (e.g. planning to network more
   when the actual gap is a missing technical skill)?
3. Are the planned actions concrete and boundable (checkable by quarter's
   end) or are they intentions that won't produce a checkable outcome?
4. Given "what's changed since last review," does the direction still
   make sense, or is this plan running on inertia rather than current
   reality?
5. Name one signal that would suggest this direction is wrong and should
   be reconsidered, not just refined.

Be honest even if the direction seems reasonable on its face — the value
of this pass is in catching what a comfortable self-review would miss.
```

## Variants
- **Post-negative-feedback variant:** weight the "what's changed" section explicitly around specific feedback received, checking whether the plan actually addresses it.
- **Long-horizon variant:** for a 3-5 year direction, add explicit market/field-trend sanity-check on whether the target role will still be relevant.

## Related prompts
- [[promotion-case-builder]]
- [[premortem-analysis]]
- [[decision-tradeoff-matrix]]

## Tips
- Bring the plan as it currently exists, warts and all — a plan cleaned up before this review defeats the purpose of a pressure test.
- Take "what would suggest this direction is wrong" seriously; career plans rarely get revisited on this specific question otherwise.

## Common mistakes
- Accepting a vague direction ("senior role somewhere") as sufficient because refining it feels like unnecessary friction — vague directions produce unfocused quarterly actions.
- Treating each quarterly review as validating the same plan indefinitely without ever asking whether changed circumstances warrant a real pivot.

## Example
Input: direction = "become a staff engineer," gap analysis lists "more visibility" but planned actions are all heads-down technical work with no cross-team component.
Expected output: flags the mismatch — visibility gaps aren't closed by more heads-down work — and suggests a specific action that would actually build cross-team visibility.

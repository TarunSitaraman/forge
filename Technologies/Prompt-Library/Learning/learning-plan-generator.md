# Learning Plan Generator

## Purpose
Build a scoped, sequenced learning plan for a genuinely new skill/domain
(not a single concept or a specific framework — see the more targeted
prompts/playbooks for those), prioritizing hands-on application over
passive consumption.

## When to Use
When starting to learn something substantial (a new domain, not a
single library) and the honest goal-setting hasn't happened yet.

## Expected Inputs
The skill/domain, why you're learning it (a specific goal, not "general
interest"), time available, and your current adjacent knowledge.

## Expected Outputs
A sequenced plan biased toward building something real early, with
checkpoints — not an exhaustive syllabus that front-loads passive
reading.

## Complete Prompt
```
Build a learning plan for this — bias toward doing over consuming.

Skill/domain: {what you're learning}
Real goal: {the specific thing you want to be able to do — not "learn X
generally"}
Time available: {hours/week, total timeframe}
Adjacent knowledge: {what you already know that's related}

Process:
1. Identify the minimum foundational concepts needed before hands-on
   work becomes productive — keep this deliberately small; the goal is
   to start building as soon as it's viable, not to front-load an
   exhaustive syllabus.
2. Define a real, scoped project to build early (within the first
   quarter of the total timeframe) that forces application of the
   foundational concepts — see `Technologies/Playbooks/building-an-mvp.md`
   framing if useful.
3. Sequence remaining topics by what the project will actually need
   next, not by a generic curriculum order.
4. Set 2-3 checkpoints tied to concrete capabilities ("can build X
   without looking anything up"), not just time elapsed.
5. Name the single most common way learners plateau in this domain, and
   what deliberate practice would push past it.
```

## Variations
- **Certification-driven variant:** add explicit mapping of the plan to
  certification exam objectives if that's the actual goal.
- **Team-onboarding variant:** frame around ramping a new team member on
  an internal domain, with real internal projects as the hands-on
  component.

## Tips
- Protect the early hands-on project from being delayed by "just one more foundational topic" — that instinct is what causes plans to stay perpetually in the prep phase.
- Revisit the plan after the first checkpoint; the real gaps you discover should reshape what comes next.

## Common Mistakes
- Front-loading weeks of passive reading/courses before any hands-on
  application, delaying the point where real learning signal (what's
  actually hard) shows up.
- Setting checkpoints as time-based ("finish by week 3") instead of
  capability-based, which doesn't reveal whether learning actually
  happened.
- Choosing a generic curriculum order instead of sequencing around what
  the chosen real project actually needs next.

## Example Usage
Input: skill = "distributed systems fundamentals," goal = "design a
reliable job queue for work project," time = "5 hours/week for 8 weeks."
Expected output: minimal foundation (message delivery guarantees,
idempotency), an early scoped project (build a toy job queue with
at-least-once delivery), checkpoints tied to explaining specific
tradeoffs, and a note that the common plateau is conflating "at least
once" with "exactly once" delivery semantics.

## Related Prompts
- [[concept-mental-model-builder]]
- [[research-question-refinement]]

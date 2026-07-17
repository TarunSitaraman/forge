# Demo Script Design

*Purpose:* Script a product/technical demo around the audience's actual pain point, with a scripted recovery if something breaks live — instead of a feature tour.

## When to use
Before any live demo (customer, stakeholder, hackathon judges), especially one with real stakes.

## Expected input
What the product/feature does, who's watching and what they care about, and the environment risk (live system, network dependency, flaky component).

## Expected output
A scripted demo flow tied to audience pain points, plus an explicit fallback plan for the highest-risk step.

## The complete prompt
```
Script this demo around the audience's actual pain point, not a feature tour.

What it does: {product/feature}
Audience: {who, what pain point they have that this addresses}
Environment risks: {anything live/network-dependent/flaky that could fail
during the demo}

Script:
1. Open with the audience's pain point stated back to them, in their
   language, before showing anything — establish why they should care in
   one sentence.
2. Sequence the demo steps so each step resolves part of the stated pain,
   not in the order features were built. Cut any step that's impressive
   but doesn't address the stated pain.
3. For the single highest-risk step (most likely to fail live): script an
   explicit fallback (a pre-recorded clip, a screenshot, a "let me show
   you the pre-run version" line) and decide in advance the trigger for
   switching to it.
4. Close with the specific outcome/number this solves for them, and the
   explicit next step you want (trial, follow-up meeting, decision).

Output: numbered demo steps with the line you'll say at each, the fallback
plan noted at the risky step, and the closing ask.
```

## Variants
- **Hackathon judging variant:** compress to under 3 minutes, front-load the most impressive/differentiating capability within the first 30 seconds.
- **Sales demo variant:** add an explicit objection-handling beat before the close.

## Related prompts
- [[presentation-narrative-arc]]

## Tips
- Identify the single highest-risk live step honestly and always script its fallback — most demo disasters are a known risk that wasn't planned for.
- Cut impressive-but-irrelevant features; judges/stakeholders remember relevance, not feature count.

## Common mistakes
- Demoing features in build order instead of pain-resolution order, losing the audience's sense of "why does this matter to me."
- Having no fallback for the one component everyone privately worried might fail (network call, live API, random data).

## Example
Input: a hackathon demo of a scheduling tool, judges care about "does this actually save time," risk = live API call to a calendar service.
Expected output: opens with the time-wasted pain point, demos the core save-time flow first, has a screen-recorded fallback ready if the live calendar API call fails, closes with the specific time-saved metric.

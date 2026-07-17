# Presentation Narrative Arc Builder

*Purpose:* Structure a technical/business presentation around a single argument the audience should walk away believing, instead of a slide-by-slide information dump.

## When to use
Before building slides, when you have the content/data but not yet a structure.

## Expected input
The raw content/points you need to cover, the audience, and the one thing you want them to believe or do after the presentation.

## Expected output
A slide-by-slide arc (title + one-sentence purpose per slide) that builds toward the single takeaway, not just an ordered list of your content.

## The complete prompt
```
Build a narrative arc for this presentation. Content dump is not the goal —
a single argument is.

Raw content/points to cover: {points}
Audience: {who, what they already know, what they care about}
The one thing they should believe/do after this: {takeaway}

Structure:
1. Opening — the hook that makes the audience care about the takeaway
   before you've argued for it (a stat, a problem they recognize, a
   question).
2. Build — order the content so each slide is evidence FOR the takeaway,
   not just adjacent information. Drop or relegate to appendix anything
   that doesn't advance the argument.
3. Address the obvious objection — the one pushback this audience will
   have; handle it before they raise it, not defensively after.
4. Close — restate the takeaway and the specific action/decision you want
   from them.

Output: numbered slide list, each with a title and one-sentence purpose
("what this slide proves, not what it contains"). Flag any of my original
content points that don't fit the arc — I'll decide whether to cut them or
move them to appendix.
```

## Variants
- **Data-heavy/technical review variant:** add explicit "what decision this data enables" framing per data slide.
- **Executive pitch variant:** compress to opening + 3 build slides + close; assume a 5-minute attention budget.

## Related prompts
- [[dense-prose-tightening]]
- [[demo-script-design]]

## Tips
- State the single takeaway explicitly before structuring — presentations without one collapse into an unordered content dump.
- Always ask for the objection-handling slide; skipping it is the most common reason Q&A derails a presentation.

## Common mistakes
- Ordering slides by the order content was gathered/researched instead of by argumentative structure.
- Trying to cover every point gathered instead of cutting what doesn't serve the single takeaway.

## Example
Input: content = a quarter's worth of metrics, takeaway = "approve headcount increase for the platform team."
Expected output: opens with the pain metric stakeholders already feel, builds through 3 metrics tying pain to team capacity, preempts the "can't we just prioritize better" objection, closes with the explicit headcount ask.

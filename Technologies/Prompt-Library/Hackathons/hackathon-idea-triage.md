# Hackathon Idea Triage

*Purpose:* Rapidly filter hackathon ideas down to the one most likely to produce a working, demo-able, judge-impressing result in the actual time available — before any code is written.

## When to use
In the first hour of a hackathon, when choosing between several candidate ideas.

## Expected input
The candidate ideas, the time available, team size/skills, and the judging criteria if known.

## Expected output
A ranked shortlist with the specific reason each idea would or wouldn't work in the time available, and a recommended scope cut for the top choice.

## The complete prompt
```
Triage these hackathon ideas for feasibility in the time we actually have.

Ideas: {list}
Time available: {hours}
Team size/skills: {who's on the team, what they're strong at}
Judging criteria (if known): {criteria, e.g. "technical difficulty,"
"business viability," "wow factor"}

For each idea:
1. Estimate the minimum viable demo scope — the smallest version that
   would still be impressive and coherent, not the full vision.
2. Flag the single riskiest technical unknown (an API that might not
   work as expected, an integration that's never been tried) — this is
   what kills hackathon projects, not lack of ambition.
3. Estimate whether the minimum viable scope is achievable by this team in
   the time available, accounting for the riskiest unknown needing to be
   validated FIRST, not last.
4. Score fit against the judging criteria given.

Rank the ideas, recommend one, and give the specific scope cut (what to
explicitly NOT build) to maximize the chance of a working demo.
```

## Variants
- **Solo hacker variant:** weight feasibility more heavily than ambition given no division of labor.
- **Sponsor-challenge variant:** add explicit check against the sponsor's specific required tech/API constraints.

## Related prompts
- [[hackathon-execution-timeline]]
- [[demo-script-design]]

## Tips
- Always identify the riskiest technical unknown and insist it gets validated in the first hour of building — this is the single biggest predictor of hackathon success or failure.
- Be honest about scope; the instinct to keep the ambitious version alive "just in case" is what causes midnight scrambles.

## Common mistakes
- Choosing the most ambitious idea without identifying its riskiest unknown, discovering at hour 20 that a core dependency doesn't work.
- Scoping the "minimum viable demo" as still too large for the actual time and team size.

## Example
Input: 3 ideas, one requiring an unfamiliar hardware SDK, 24 hours, team of 2.
Expected output: flags the hardware SDK idea as highest-risk (unknown API behavior), recommends a lower-risk idea instead or requires validating the SDK in the first 2 hours before committing.

# Salary Research Structuring

*Purpose:* Turn scattered comp data points into a defensible target range
and negotiation anchor — flagging where the data is thin or unreliable,
rather than presenting a false-confidence single number.

## When to use
While filling out `Systems/Career/salary-research-worksheet.md`, or
before any negotiation conversation.

## Expected input
The data points you've gathered (source, numbers, how recent), the role/
level/location, and your own comp history/constraints.

## Expected output
A defensible target range with confidence noted per data source, and the
specific gaps in your data worth filling before negotiating.

## The complete prompt
```
Help me turn this comp research into a defensible target range — flag
weak data rather than presenting false confidence.

Role/level/location: {details}
Data points gathered: {source, base/bonus/equity, how recent, for each}
My current comp / constraints: {current total comp, minimum acceptable,
any competing offer}

Process:
1. Weight each data point by recency and source reliability (a recent
   number from someone actually in a comparable role at this company
   beats an aggregated site average from 2 years ago) — state the
   weighting reasoning.
2. Identify the biggest gap in my data — the thing I'm least confident
   about (often equity valuation or level-mapping across companies) —
   and suggest a specific way to fill it (a specific site, a specific
   type of person to ask).
3. Give a target range (not a single number) with the reasoning, and
   flag if my data is too thin to be confident in it yet.
4. Sanity-check my stated minimum acceptable and target against the
   range — are they realistic given the data, or anchored on something
   outdated/unrepresentative?
5. Do NOT fabricate a specific company's comp figures if I haven't given
   you real data — work only with what I've provided, and say so
   explicitly if it's insufficient to produce a confident range.
```

## Variants
- **Competing-offer variant:** add explicit framing on how to use a real competing offer as an anchor without misrepresenting it.
- **First-job/early-career variant:** weight level-mapping and location-adjustment guidance more heavily, since the data is often thinner at this stage.

## Related prompts
- [[offer-comparison-prompt]]
- [[decision-tradeoff-matrix]]

## Tips
- Bring real data points, even rough ones — this prompt is for synthesizing YOUR gathered evidence, not generating comp numbers from the model's general knowledge, which would be unreliable and unverifiable.
- Take the "biggest data gap" step seriously; walking into a negotiation with an unfilled major gap (often equity valuation) is a common and avoidable mistake.

## Common mistakes
- Asking the model to just "tell me what X role pays" without providing real gathered data — treat this as a synthesis tool for your research, not a source of truth itself.
- Anchoring on a single outlier data point (e.g. one very high number seen online) without weighting it against more representative sources.

## Example
Input: 3 data points of varying recency, a stated minimum acceptable that's below the lowest data point.
Expected output: flags that the stated minimum may be under-anchored given the data, gives a weighted target range, flags equity valuation as the weakest data point to firm up.

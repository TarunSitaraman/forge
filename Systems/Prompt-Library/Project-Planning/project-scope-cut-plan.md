# Project Scope Cut Plan

*Purpose:* Decide, before a deadline crunch hits, exactly what gets cut and in what order — so scope decisions are made deliberately in advance, not improvised under pressure.

## When to use
At project kickoff (as a preemptive plan) or the moment a deadline risk becomes visible.

## Expected input
The full planned scope, the hard deadline, and what "must ship" vs. "nice to have" means for this project's actual purpose.

## Expected output
A ranked cut list — the order features get dropped if time runs short — decided now, with the reasoning for the ranking.

## The complete prompt
```
Build a scope cut plan for this project, decided now rather than under
deadline pressure.

Full planned scope: {features/work items}
Hard deadline: {date}
What this project must actually achieve to be considered successful:
{core purpose}

For each feature/work item:
1. Classify as MUST (project fails its core purpose without it), SHOULD
   (valuable but the core purpose survives without it), or COULD (nice to
   have, easily deferred).
2. For SHOULD/COULD items, rank the cut order — which goes first if time
   runs short, second, etc. — based on which cuts hurt the least relative
   to the core purpose.
3. For each MUST item, flag if its current scope could be reduced (a
   simpler version that still satisfies the core purpose) as a fallback
   before considering it truly uncuttable.
4. State the specific checkpoint (date or % of timeline) at which we
   should evaluate whether cuts are needed, rather than waiting until
   the deadline is imminent.

Output the classification and cut order as a simple ranked list.
```

## Variants
- **Client-facing project variant:** add an explicit communication plan for each cut tier (what to tell the client if it's invoked).
- **Solo-side-project variant:** bias more aggressively toward cutting, since there's no team coordination cost to changing scope late.

## Related prompts
- [[decision-tradeoff-matrix]]
- [[hackathon-execution-timeline]]

## Tips
- Decide the cut order before the pressure hits — decisions made in a crunch are worse than decisions made with a clear head.
- Set the evaluation checkpoint explicitly; scope risk is usually visible well before the deadline if someone's watching for it.

## Common mistakes
- Treating every planned feature as equally essential, leaving no clear plan when a cut becomes necessary.
- Waiting until the deadline is imminent to have the scope conversation, when earlier warning signs were already visible.

## Example
Input: a product launch with 8 planned features, deadline in 6 weeks, core purpose = "let a user complete a purchase end to end."
Expected output: classifies checkout and payment as MUST, wishlist and reviews as SHOULD, social sharing as COULD, ranks cut order accordingly, sets a checkpoint at week 4.

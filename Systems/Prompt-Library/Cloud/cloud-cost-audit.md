# Cloud Cost Audit

*Purpose:* Turn a cloud bill into a ranked list of concrete savings actions, tied to actual usage patterns, not generic "right-size your instances" advice.

## When to use
Monthly/quarterly cost review, or when a bill spikes unexpectedly.

## Expected input
Cost breakdown by service (from your billing console export), current architecture summary, and known usage patterns (peak hours, batch jobs, dev/staging always-on).

## Expected output
A ranked list of savings actions, each with estimated impact and the specific risk of making that change.

## The complete prompt
```
Audit this cloud spend and give me a ranked action list, not generic advice.

Cost breakdown by service: {billing export summary}
Architecture: {brief description}
Usage patterns: {peak hours, batch/dev-staging always-on, known idle resources}
Constraints: {what can't change, e.g. compliance requiring a specific region}

For each of the top cost drivers:
1. Name the specific waste mechanism (e.g. "dev environment compute running
   24/7 at production instance size," not "consider right-sizing").
2. Estimate the savings (rough % of that line item is fine, state your
   assumption).
3. State the implementation effort (config change / architecture change /
   requires downtime) and the risk of the change.
4. Rank all actions by savings-per-effort, highest first.

Flag separately: any cost driver that looks like a bug (e.g. runaway logging,
an orphaned resource still running) rather than a legitimate architectural
tradeoff — these are zero-risk to fix and should be called out first.
```

## Variants
- **Reserved-capacity variant:** add explicit reserved-instance/commitment analysis against actual usage stability.
- **Multi-cloud variant:** add a cross-provider cost comparison for portable workloads.

## Related prompts
- [[azure-resource-review]]
- [[tradeoff-decision-matrix]]

## Tips
- Paste the actual billing breakdown, even roughly — cost audits on vague descriptions produce vague savings.
- Always ask for the "looks like a bug" flag separately; those are the free wins.

## Common mistakes
- Recommending architecture changes with real risk before exhausting zero-risk fixes (orphaned resources, oversized dev environments).
- Ignoring effort/risk and ranking purely by savings size — a 5% savings requiring a rewrite may not be worth it.

## Example
Input: billing shows staging environment costing 40% of production despite near-zero traffic.
Expected output: flags staging as running production-sized instances 24/7, estimates 70%+ savings from scheduled shutdown + downsizing, ranks it as the top zero-risk action.

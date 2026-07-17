# Project Dependency & Risk Mapping

*Purpose:* Surface the project's real critical path and external dependencies before planning a timeline around wishful parallelism.

## When to use
At project planning time, before committing to a timeline, especially for projects with multiple contributors or external dependencies.

## Expected input
The list of work items/milestones, who/what each depends on, and any external dependencies (another team, a vendor, an API, a decision pending elsewhere).

## Expected output
A dependency graph description identifying the critical path, and a ranked list of the riskiest dependencies with a mitigation for each.

## The complete prompt
```
Map the dependencies and risks for this project's plan.

Work items/milestones: {list}
Known dependencies: {what depends on what, including external ones — other
teams, vendors, APIs, pending decisions}
Team/resource constraints: {who's available when}

Analyze:
1. Identify the critical path — the sequence of dependent items that
   determines the minimum possible timeline, even if other work could be
   parallelized around it.
2. Flag every EXTERNAL dependency (outside this team's control) explicitly
   — these carry the highest schedule risk since they can't be
   accelerated by working harder.
3. For each external dependency, estimate the risk (how likely it slips,
   based on what's known) and a mitigation: can it be de-risked early (a
   spike/proof of concept), have a fallback plan, or does the whole
   timeline hinge on it with no fallback?
4. Identify any items currently planned in parallel that actually have a
   hidden dependency on each other (same person needed for both, or a
   data dependency not yet made explicit).

Output the critical path explicitly, then the ranked risk list with
mitigation for each.
```

## Variants
- **Cross-team variant:** add an explicit communication/check-in cadence recommendation for each external dependency.
- **Vendor-dependency variant:** add contract/SLA review as part of risk assessment for vendor dependencies.

## Related prompts
- [[project-scope-cut-plan]]
- [[decision-tradeoff-matrix]]

## Tips
- Name external dependencies explicitly and separately from internal ones — they deserve disproportionate risk attention since you can't just "work harder" to accelerate them.
- Check for hidden resource contention (same person on two "parallel" tasks) — this is a common silent cause of slipped timelines.

## Common mistakes
- Planning a timeline assuming full parallelism without checking for shared-resource or hidden data dependencies between "independent" tasks.
- Treating an external dependency's promised date as certain instead of assigning it real risk and a fallback.

## Example
Input: a project where the frontend team's work depends on an API contract from a backend team that hasn't been finalized.
Expected output: flags the API contract as the critical-path blocker and highest-risk external dependency, recommends finalizing the contract as a spike in week 1 rather than letting both teams build in parallel against an assumed shape.

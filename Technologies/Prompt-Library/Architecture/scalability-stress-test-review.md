# Scalability Stress-Test (Design Walkthrough)

*Purpose:* Pressure-test a proposed design against explicit load, failure, and growth scenarios before it's built, catching bottlenecks on paper instead of in production.

## When to use
After a design is drafted but before implementation begins, especially for anything expected to handle significant load or grow 10x.

## Expected input
The design (diagram or description), current and projected scale (RPS, data volume, growth rate), and known constraints (budget, latency SLA).

## Expected output
A list of specific breaking points under load/growth, ranked by how soon they'd be hit, with mitigation for each.

## The complete prompt
```
Stress-test this design against scale and failure, on paper, before we build it.

Design: {description/diagram}
Current scale: {RPS, data volume, concurrent users}
Projected scale (12 months): {growth targets}
Latency/availability SLA: {SLA}
Budget/infra constraints: {constraints}

Walk through these scenarios explicitly and name the specific component
that breaks first in each, not a generic "this might not scale":
1. 10x current traffic, sustained.
2. A sudden 10x traffic spike (not sustained) — what queues/buffers first,
   what falls over.
3. The single point of failure — which component's outage takes down the
   whole system, and what's the blast radius.
4. Data growth at the projected rate for 12 months — what breaks first:
   query performance, storage cost, index size, backup/restore time.
5. A dependency (third-party API, database) going down or slowing to 10x
   latency — does the system degrade gracefully or cascade-fail.

For each breaking point: name it specifically (component + mechanism, e.g.
"connection pool exhaustion on service X at ~Y RPS"), and give the cheapest
mitigation that defers it past the 12-month horizon.
```

## Variants
- **Cost-focused variant:** replace scenario 4 with explicit cost-at-scale projection instead of just performance.
- **Multi-region variant:** add a region-failure scenario.

## Related prompts
- [[architecture-decision-record]]
- [[system-design-interview-style]]

## Tips
- Give real numbers, not "high traffic" — specific RPS/data volume produces specific, checkable breaking points.
- Treat this as a design-time exercise; running it after launch is triage, not stress-testing.

## Common mistakes
- Accepting vague answers like "the database might become a bottleneck" without a specific mechanism and threshold.
- Skipping the graceful-degradation scenario — cascading failures are often the costliest class of incident.

## Example
Input: a design with a single Postgres instance for both reads and writes, projected 10x traffic growth.
Expected output: identifies read replica lag / connection exhaustion as the first breaking point at a specific RPS estimate, recommends read replicas + connection pooling as the deferring mitigation.

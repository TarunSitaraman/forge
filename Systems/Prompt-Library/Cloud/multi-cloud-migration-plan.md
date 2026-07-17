# Cloud Migration Plan (Provider-to-Provider or On-Prem-to-Cloud)

*Purpose:* Produce a sequenced, risk-ranked migration plan instead of a "lift and shift everything" plan that ignores dependency order and rollback risk.

## When to use
When planning a migration between cloud providers, or from on-prem to cloud, for a non-trivial system.

## Expected input
Current architecture (services, data stores, dependencies), migration target, hard constraints (deadline, downtime tolerance, compliance).

## Expected output
A phased migration plan ordered by dependency and risk, with an explicit rollback plan per phase.

## The complete prompt
```
Plan this migration: {current architecture} → {target}.
Constraints: {deadline, downtime tolerance, compliance/data residency requirements}
Dependencies: {which components depend on which}

Produce:
1. Dependency-ordered phases — what must move first because other things
   depend on it (usually: data layer and auth before stateless services).
2. For each phase: what moves, the cutover mechanism (blue-green, strangler,
   big-bang), and why that mechanism fits this phase's risk profile.
3. Rollback plan per phase — the specific condition that triggers rollback,
   and how fast rollback can happen.
4. The single riskiest phase, and a mitigation that specifically de-risks it
   (e.g. dual-write period, canary traffic percentage).
5. What to explicitly NOT migrate yet (e.g. legacy batch jobs) and why
   deferring is fine.

Do not propose a single-phase "migrate everything" plan for any system with
real dependencies or a downtime constraint tighter than a full maintenance
window.
```

## Variants
- **Data-residency-constrained variant:** add explicit data classification step (what must stay in-region) before phasing.
- **Zero-downtime variant:** require dual-write/strangler pattern for every phase touching a live data store.

## Related prompts
- [[cloud-cost-audit]]
- [[architecture-decision-record]]

## Tips
- Name the actual dependency graph, even roughly — migration order mistakes are the single biggest source of migration incidents.
- Insist on a rollback trigger condition per phase, not just "we can roll back if needed."

## Common mistakes
- Migrating stateless services before the data layer they depend on, causing cross-cloud latency or breakage mid-migration.
- Treating rollback as a generic capability instead of defining the specific condition and time window per phase.

## Example
Input: migrating a 3-tier app (Postgres, API, frontend) from AWS to Azure, with a 4-hour downtime budget.
Expected output: phases as (1) stand up Postgres replica on Azure with ongoing replication, (2) cut over API with dual-write fallback, (3) cutover DNS, (4) decommission AWS — with rollback defined at each step.

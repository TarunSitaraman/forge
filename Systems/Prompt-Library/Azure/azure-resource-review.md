# Azure Resource Configuration Review

*Purpose:* Audit an Azure resource's configuration (networking, IAM, scaling) against production-readiness standards before it goes live, not after an incident.

## When to use
Before promoting an Azure resource (App Service, AKS cluster, Function App, Storage Account) from dev/staging to production.

## Expected input
The resource's ARM/Bicep template or portal configuration export, and its intended production traffic/security profile.

## Expected output
A pass/fail checklist against production-readiness criteria, with specific fixes for each failure.

## The complete prompt
```
Review this Azure resource configuration for production readiness.

Resource type: {e.g. App Service, AKS, Function App, Storage Account}
Configuration: {ARM/Bicep/portal export}
Intended traffic/security profile: {expected load, public/internal, data sensitivity}

Check explicitly, pass/fail each with the specific setting responsible:
1. Identity & access — managed identity used instead of connection-string
   secrets where possible; least-privilege RBAC, not Owner/Contributor
   assigned broadly.
2. Networking — private endpoints/VNet integration for anything not meant
   to be public; NSG rules scoped, not 0.0.0.0/0.
3. Scaling — autoscale rules present and sane for the expected traffic
   profile; no hardcoded single-instance config for production load.
4. Secrets — nothing in app settings/environment variables that should be
   in Key Vault.
5. Observability — diagnostic settings/Application Insights actually
   wired up, not just available.
6. Cost guardrails — budget alerts or quotas present for anything
   autoscaling or usage-billed.

For each fail: give the exact fix (setting name + correct value or the
Bicep/ARM snippet), not just "improve security."
```

## Variants
- **AKS-specific variant:** add pod security standards, network policies, and node pool isolation checks.
- **Compliance variant:** add data residency and audit-log-retention checks for regulated workloads.

## Related prompts
- [[cloud-cost-audit]]
- [[architecture-decision-record]]

## Tips
- Paste the actual template/export — reviews against a description miss the specific misconfigured setting.
- Run this before the resource takes production traffic, not as an incident postmortem.

## Common mistakes
- Treating "it works in staging" as sufficient evidence for production readiness — staging rarely has production's traffic or threat profile.
- Granting Contributor/Owner roles broadly instead of scoping RBAC to the specific actions needed.

## Example
Input: an App Service config with a connection string in app settings and no VNet integration, intended for a customer-facing production API.
Expected output: fails identity (should use managed identity + Key Vault) and networking (should have private endpoint or at minimum restricted access), with the specific Bicep changes.

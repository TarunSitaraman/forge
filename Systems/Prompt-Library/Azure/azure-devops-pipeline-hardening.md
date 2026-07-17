# Azure DevOps / GitHub Actions Pipeline Hardening

*Purpose:* Harden a CI/CD pipeline against the most common real-world failure and security gaps before it's trusted with production deploys.

## When to use
Before a new pipeline first deploys to production, or during a security review of an existing pipeline.

## Expected input
The pipeline YAML (Azure DevOps or GitHub Actions), and what it deploys to (environment, permissions it needs).

## Expected output
A hardening checklist with specific YAML changes, ordered by risk.

## The complete prompt
```
Harden this CI/CD pipeline before it's trusted with production deploys.

Pipeline YAML: {yaml}
Deploys to: {environment, what access it needs}

Check, in order of risk:
1. Secrets handling — are secrets pulled from a vault/secure store at
   runtime, or hardcoded/passed as plain variables? Any secret that could
   leak into logs?
2. Permission scope — does the pipeline's service connection/token have
   only the permissions it needs, or broad subscription/org access?
3. Approval gates — is there a required manual approval before production
   deploy, or does merge-to-main auto-deploy with no gate?
4. Supply chain — are third-party actions/tasks pinned to a commit SHA
   (not a mutable tag), and is there any dependency installation step that
   isn't using a lockfile?
5. Rollback — is there an automated or one-command rollback path if the
   deploy fails health checks?

For each gap: give the specific YAML change to fix it, not a general
recommendation.
```

## Variants
- **Open-source project variant:** add explicit checks for pull_request_target misuse and fork PR permission scoping (GitHub Actions specific).
- **Regulated-environment variant:** add mandatory audit trail / who-approved-what logging.

## Related prompts
- [[azure-resource-review]]
- [[production-incident-triage]]

## Tips
- Paste the real YAML — pipeline hardening issues are specific to exact steps and permission blocks, not generalizable from a description.
- Prioritize the supply-chain check for any pipeline using third-party actions; it's the most commonly exploited and least monitored.

## Common mistakes
- Adding a production approval gate but leaving the service connection with subscription-wide Contributor access.
- Pinning first-party actions but leaving third-party actions on a mutable `@main`/`@v1` tag.

## Example
Input: a GitHub Actions workflow deploying to Azure on every push to main, using a third-party action pinned to `@v2`, no approval gate.
Expected output: flags missing approval gate and unpinned action as the top two risks, with the exact YAML for an environment protection rule and SHA pinning.

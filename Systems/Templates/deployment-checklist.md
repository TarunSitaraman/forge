# Template: Deployment Checklist

*Guidance: a deployment checklist exists to make a risky, infrequent
action (shipping to production) as boring and repeatable as possible.
Fill in the specifics for your actual system — a generic checklist that
doesn't name your real health checks and rollback command isn't worth
much at 2am.*

---

# Deployment Checklist: {System / Service Name}

**Deployment date:** {date} · **Owner:** {name} · **Change:** {one-line
summary of what's being deployed}

## Pre-deployment

- [ ] Change reviewed and approved (link to PR/review)
- [ ] Tests passing on the exact commit being deployed
- [ ] Database migrations (if any) reviewed for backward compatibility —
      can the previous app version run against the new schema during
      rollout?
- [ ] Feature flags/config for this change set to the intended state
- [ ] Stakeholders notified if this change is user-visible or has a
      maintenance window
- [ ] Rollback plan confirmed and the specific rollback command/process
      identified — not assumed to "just work"

## During deployment

- [ ] Deployment started via {the actual mechanism — CI/CD pipeline,
      manual command}
- [ ] Health checks passing post-deploy: {the specific endpoint/dashboard
      to check}
- [ ] Error rate / latency dashboards watched for {specific time window}
      after rollout
- [ ] Canary/gradual rollout stage confirmed healthy before full rollout
      (if applicable)

## Post-deployment

- [ ] Smoke test of the actual changed functionality in production
- [ ] Monitoring/alerting confirmed active for anything new this
      deployment introduced
- [ ] Deployment recorded (changelog, deployment log, or equivalent)
- [ ] Stakeholders notified of completion

## Rollback trigger

{The specific condition that would trigger a rollback — an error rate
threshold, a failed health check, a specific user-facing symptom — decided
now, not improvised if something looks off.}

## Rollback steps

{The exact steps/commands to roll back this specific deployment.}

---

## Example

For a deployment including a database migration adding a nullable
column: the pre-deployment check confirms the new column is nullable
(so the previous app version tolerates it), the rollback plan notes that
rolling back the app is safe without a schema rollback since the column
is additive, and the rollback trigger is defined as "error rate >2% for
5 minutes post-deploy."

## Best practices for this template
- Fill in the actual rollback command before deploying, not after
  something goes wrong — improvising a rollback under pressure is a
  common cause of a bad deployment becoming a worse incident.
- Define the rollback trigger threshold in advance — deciding "does this
  look bad enough to roll back" in the moment leads to inconsistent,
  often too-slow decisions.
- For database migrations, always check backward compatibility with the
  pre-deployment app version — a migration that requires the new code to
  already be running makes rollback impossible.

## Related Templates
- [`sprint-planning.md`](sprint-planning.md)
- [`design-decision-record.md`](design-decision-record.md) — for the decision behind a significant infrastructure or migration change

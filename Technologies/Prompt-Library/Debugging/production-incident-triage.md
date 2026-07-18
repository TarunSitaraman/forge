# Production Incident Triage

*Purpose:* Structure the first 15 minutes of a production incident — stop the bleeding, gather the right signals, and avoid actions that make it worse — before root-causing.

## When to use
The moment you're paged or notice a production incident, before deep investigation begins.

## Expected input
Alert/symptom, current impact (who/what is affected), system context (recent deploys, known fragile components).

## Expected output
An immediate triage plan: mitigation options ranked by risk/speed, what to check first, and what to communicate to stakeholders right now.

## The complete prompt
```
Production incident in progress. I need triage, not root cause analysis, first.

Symptom / alert: {symptom}
Impact: {who/what is affected, severity}
Recent changes: {deploys, config changes, traffic changes in last 24h}
System context: {relevant architecture}

Give me, in order:
1. Immediate mitigation options (rollback, feature flag, scale up, circuit
   break) ranked by (a) how fast they stop the bleeding and (b) how
   reversible/safe they are. Recommend one.
2. The 3 signals to check right now to confirm or rule out the most likely
   cause given recent changes (favor "did we just deploy something" as the
   first hypothesis).
3. A one-paragraph stakeholder update I can send immediately: what's known,
   what's being done, next update time.
4. What NOT to do right now (e.g. "don't restart the database, that will
   drop in-flight transactions") given this system context.

Do not attempt full root cause analysis yet — that comes after mitigation.
```

## Variants
- **Data-loss-risk variant:** add explicit "is this reversible" gate before any mitigation action.
- **Multi-team incident variant:** add an incident-commander-style ownership/communication assignment step.

## Related prompts
- [[root-cause-five-whys]]

## Tips
- Always state recent deploys/changes — most incidents trace to something that changed, and this focuses the triage immediately.
- Insist on the "what NOT to do" section; it prevents well-intentioned actions that worsen the incident.

## Common mistakes
- Jumping straight to root cause analysis while the incident is still actively causing damage.
- Recommending a rollback without checking whether it's safe given in-flight state (migrations, queued jobs).

## Example
Input: symptom = "API error rate spiked to 40%," recent change = "deploy 20 minutes ago."
Expected output: recommends immediate rollback as top mitigation, names checking error logs for the new deploy's code path as the first signal, drafts a stakeholder update.

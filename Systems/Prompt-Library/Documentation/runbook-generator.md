# Operational Runbook Generator

*Purpose:* Produce a runbook that an on-call engineer with no prior context can actually execute at 3am under pressure — not a description of how the system works.

## When to use
Before a new service goes on-call rotation, or when an existing runbook has failed to help during a real incident.

## Expected input
The system/service, its known failure modes (from past incidents if available), and the actions available to an on-call responder (restart, scale, rollback, escalate).

## Expected output
A runbook organized by symptom, each with a diagnostic check and a specific action — not a general architecture description.

## The complete prompt
```
Generate a runbook for on-call use — must be executable by someone
unfamiliar with this system, under time pressure.

System: {service/system description}
Known failure modes: {past incidents, alerts that fire, common symptoms}
Available actions: {restart, scale, rollback, feature-flag, escalate to whom}

For each known failure mode/alert:
1. Symptom — the exact alert name or observable symptom, so the
   responder can match it immediately.
2. Diagnostic check — the specific command/dashboard/log query to
   confirm this is actually the cause (not a guess).
3. Action — the specific, executable step (exact command or exact UI
   path), not "investigate further."
4. Escalation trigger — the specific condition under which to page
   someone else instead of continuing to try fixes, and who to page.
5. Do NOT include general architecture explanation — link to it
   separately if needed, but the runbook itself should be pure
   symptom → action.

Format as a table: Symptom | Diagnostic | Action | Escalate if.
```

## Variants
- **New-service variant:** since no past incidents exist yet, derive failure modes from the architecture's known failure points (dependencies, single points of failure) instead.
- **Post-incident variant:** append the runbook with the specific gap this incident revealed, so it improves the runbook rather than just documenting the incident.

## Related prompts
- [[production-incident-triage]]
- [[root-cause-five-whys]]

## Tips
- Insist on exact commands/dashboard links, not "check the logs" — vague runbooks fail exactly when they're needed most.
- Always add the new failure mode to the runbook right after a real incident, while the specifics are still fresh.

## Common mistakes
- Writing a runbook that explains how the system works instead of what to do when it breaks — useful for onboarding, useless at 3am.
- Missing the escalation trigger, leaving an unfamiliar responder stuck trying fixes indefinitely instead of paging someone with more context.

## Example
Input: a service with a known "queue backup" failure mode from 2 past incidents.
Expected output: a table row: Symptom = "QueueDepthHigh alert," Diagnostic = "check consumer lag dashboard link," Action = "scale consumer replicas via `kubectl scale ...`," Escalate if = "lag not decreasing after 10 min, page data-platform on-call."

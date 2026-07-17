# Container Debugging

## Purpose
Systematically diagnose a misbehaving running container (crash loop,
unexpected resource usage, unreachable network) using the actual
container's state, rather than guessing from the application code alone.

## When to Use
A container is crash-looping, using unexpected CPU/memory, or unreachable
over the network, and the cause isn't obvious from application logs
alone.

## Expected Inputs
The symptom, the Dockerfile/image, and what you've already checked
(logs, `docker inspect` output, resource limits).

## Expected Outputs
A prioritized diagnostic path (what to check next, in order) tied to the
specific symptom class, not a generic troubleshooting checklist.

## Complete Prompt
```
Help me debug this container issue systematically.

Symptom: {crash loop / high resource usage / unreachable / other}
Dockerfile/image: {relevant contents}
Already checked: {logs, docker inspect output, resource limits, etc. —
paste what you have}

Diagnose by symptom class:
1. If crash-looping: is this an application error (check logs for the
   actual exception/exit code) or an infra issue (OOM-killed — check
   exit code 137 and memory limits, or a failed healthcheck causing
   restart)? Distinguish these before proposing a fix.
2. If high resource usage: is this the application's real workload, a
   runaway process (check `docker stats` and `docker exec ... ps aux`
   for the actual process consuming resources), or a missing resource
   limit letting a leak run unbounded?
3. If unreachable: is this a port mapping issue (`docker inspect` port
   bindings), a network mode/DNS issue (check which Docker network the
   container is on and whether the target is reachable from inside via
   `docker exec ... curl/ping`), or an application not actually listening
   on the expected interface (`0.0.0.0` vs `127.0.0.1`)?
4. Give the SPECIFIC next command to run for the given symptom, not a
   generic "check the logs" — tell me exactly what evidence to gather
   next given what's already been checked.
```

## Variations
- **Kubernetes variant:** substitute `kubectl describe pod` / `kubectl
  logs --previous` for the equivalent Docker CLI evidence-gathering
  steps.
- **Networking-focused variant:** add explicit DNS resolution and
  service-discovery checks for multi-container/Compose setups.

## Tips
- Always get the exact exit code before theorizing about a crash loop's cause — it usually tells you directly (137 = OOM killed).
- Use `docker exec` to inspect the live container's actual state rather than reasoning from the Dockerfile alone.

## Common Mistakes
- Assuming a crash loop is an application bug without first checking the
  exit code for an OOM kill (137) or a failed healthcheck restart.
- Debugging resource usage by reading code instead of using `docker
  stats`/`docker exec ps aux` to see what's actually consuming resources
  right now.
- Not checking which Docker network a container is actually on before
  assuming a networking issue is DNS-related.

## Example Usage
Input: symptom = "container keeps restarting," already checked = "logs
show nothing unusual right before restart." Expected output: asks for
the exit code specifically, diagnoses OOM kill (137) once provided,
recommends checking the container's memory limit against actual usage
via `docker stats` before the crash.

## Related Prompts
- [[dockerfile-review]]
- [[production-incident-triage]]
- [[root-cause-five-whys]]

# Dockerfile Review

## Purpose
Review a Dockerfile against production-readiness standards — image size,
build cache efficiency, security — using the concrete gaps documented in
`../Docs/docker.md`, not generic "containerize this" advice.

## When to Use
Before a Dockerfile goes into a CI/CD pipeline or production deployment,
or when an image is unexpectedly large or slow to build.

## Expected Inputs
The Dockerfile, and what the container runs (a web service, a batch job,
a CLI tool) plus its expected deployment environment.

## Expected Outputs
A pass/fail review against specific criteria, each failure paired with
the exact line/instruction to change.

## Complete Prompt
```
Review this Dockerfile for production readiness.

Dockerfile: {contents}
What it runs: {service type}
Deployment environment: {e.g. Kubernetes, ECS, a single VM}

Check explicitly, pass/fail with the fix if failed:
1. Layer caching — are rarely-changing steps (base image, dependency
   install) ordered before frequently-changing ones (application code
   copy)? A `COPY . .` before dependency install invalidates the cache
   on every code change.
2. Image size — is this a multi-stage build separating build-time
   dependencies from the runtime image? Is the base image minimal
   (slim/alpine/distroless) for what's actually needed?
3. User — does the container run as a non-root `USER`, or default to
   root?
4. Secrets — is anything sensitive baked into a layer via `ENV`, `ARG`,
   or a `COPY`'d file, rather than injected at runtime?
5. Health check — is there a `HEALTHCHECK` instruction so orchestrators
   can detect a running-but-unhealthy container?
6. Pinning — are the base image and any installed package versions
   pinned, or will a rebuild silently pull a different version later?

For each failure, give the exact Dockerfile change.
```

## Variations
- **CI-build-speed variant:** weight layer-caching issues most heavily,
  since these directly affect CI pipeline duration at scale.
- **Compliance/regulated variant:** add explicit base-image provenance
  and vulnerability-scanning requirement checks.

## Tips
- Run this review before a Dockerfile ever reaches a CI pipeline — cache-inefficiency costs compound silently across every build.
- Cross-check findings against `../Docs/docker.md` for the underlying reasoning behind each check.

## Common Mistakes
- Accepting a Dockerfile that "works" without checking image size or
  cache efficiency, which compounds into real CI cost at scale.
- Missing the non-root `USER` check — this is the single most common
  security gap in hand-written Dockerfiles.
- Reviewing syntax correctness without checking whether the image
  actually matches the deployment environment's real needs (e.g. missing
  a `HEALTHCHECK` an orchestrator depends on).

## Example Usage
Input: a Dockerfile with `COPY . .` immediately after `FROM`, before
`RUN npm install`, no `USER` instruction. Expected output: flags the
cache-invalidation ordering issue with the specific fix (copy
`package.json` first, install, then copy the rest), and flags the
missing non-root user with the exact `USER node` line to add.

## Related Prompts
- [[container-debugging]]
- [[azure-devops-pipeline-hardening]]

# API Versioning Strategy Decision

## Purpose
Choose an explicit versioning approach for an API before the first
breaking change forces a rushed decision — this is a design-time
decision, not something to improvise once a client is already broken.

## When to Use
When designing a new API's evolution strategy, or realizing an existing
API has no versioning plan right as a breaking change becomes necessary.

## Expected Inputs
The API's consumer type (internal/partner/public), how often breaking
changes are anticipated, and any platform constraints (mobile clients
that can't be forced to update quickly, for instance).

## Expected Outputs
A specific versioning mechanism recommendation (URL path, header,
content negotiation) with the deprecation policy that goes with it — not
just "use semantic versioning" as an answer.

## Complete Prompt
```
Recommend a versioning strategy for this API, reasoned from the actual
consumer constraints.

Consumers: {internal / partner / public, and how many/who}
Anticipated breaking-change frequency: {rare / occasional / frequent}
Client update constraints: {e.g. "mobile clients can take months to
update," "internal services deploy together," "partners integrate once
and rarely revisit"}

Recommend:
1. The specific mechanism — URL path versioning (`/v2/...`), a custom
   header, or content negotiation (`Accept` header) — reasoned against
   the consumer constraints, not a default preference. URL versioning is
   simplest for slow-to-update external clients; header-based can suit
   fast-iterating internal consumers.
2. The deprecation policy: how long old versions stay supported after a
   new one ships, given the client update constraints — a policy that
   doesn't account for how slowly the slowest real consumer updates will
   break them.
3. How breaking vs. non-breaking changes are distinguished in practice
   for this API (adding an optional field = non-breaking; removing or
   renaming a field = breaking) — state the concrete rule so future
   contributors don't have to guess.
4. How the deprecation is communicated and enforced (changelog, sunset
   headers, monitoring for old-version usage before removal) — a policy
   with no enforcement mechanism tends to let "deprecated" versions live
   forever.
```

## Variations
- **No-existing-strategy variant:** for an API that's already shipped
  without a versioning plan, add an explicit migration step for
  introducing versioning without breaking current consumers immediately.
- **Partner-API variant:** add explicit advance-notice period
  requirements, since partner integrations often need contractual lead
  time for breaking changes.

## Tips
- Decide this before the first breaking change is needed — retrofitting a versioning strategy under deadline pressure produces worse decisions.
- Pair the deprecation policy with an actual enforcement mechanism, or it will never be enforced.

## Common Mistakes
- Choosing a versioning mechanism based on preference/trend rather than
  the actual client update speed constraints.
- Setting a deprecation policy without an enforcement mechanism, letting
  "deprecated" versions run indefinitely because no one's tracking usage.
- Not defining the breaking-vs-non-breaking rule explicitly, leading to
  inconsistent judgment calls across contributors over time.

## Example Usage
Input: consumers = "public API, some mobile clients that update over
months," breaking-change frequency = "occasional." Expected output:
recommends URL path versioning for clarity and cache-friendliness, a
12-month deprecation window given mobile update lag, and sunset-header +
usage-monitoring as the enforcement mechanism.

## Related Prompts
- [[api-design-from-scratch]]
- [[api-contract-design-review]]

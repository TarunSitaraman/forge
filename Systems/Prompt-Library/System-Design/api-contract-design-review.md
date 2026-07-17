# API Contract Design Review

*Purpose:* Review a proposed API contract (REST/GraphQL/RPC) for the mistakes that are expensive to fix after clients integrate — versioning, error handling, and consistency — before it ships.

## When to use
Before publishing a new API or a breaking-capable change to an existing one.

## Expected input
The API contract (endpoint definitions, request/response schemas), who consumes it (internal service, external partners, public), and expected evolution (will it need new fields/versions soon).

## Expected output
A pass/fail review against contract-design standards with the specific fix for each failure.

## The complete prompt
```
Review this API contract before it ships — focus on what's expensive to
fix after clients integrate.

Contract: {endpoints, request/response schemas}
Consumers: {internal / external partners / public}
Expected evolution: {likely new fields, versions, deprecations}

Check, pass/fail with the specific fix if failed:
1. Versioning strategy — is there an explicit mechanism (URL version,
   header, field) for evolving this without breaking existing clients?
2. Error contract — are errors structured consistently (status code +
   machine-readable error code + human message), or ad hoc per endpoint?
3. Field evolution safety — are response schemas additive-safe (new
   optional fields won't break clients), and is there a stated policy
   on removing/renaming fields?
4. Consistency with existing APIs — does naming, pagination, and
   auth pattern match the rest of the API surface, or does it introduce
   a new convention for no reason?
5. Idempotency — for any mutating endpoint, is there an idempotency
   mechanism (key, natural idempotency) for safe retries?

For external/public consumers, apply this review more strictly — internal
APIs can tolerate faster iteration and breaking changes with coordination;
external contracts cannot.
```

## Variants
- **GraphQL variant:** replace versioning check with deprecation-directive and schema-evolution review.
- **Webhook/event contract variant:** add explicit delivery-guarantee (at-least-once vs. exactly-once) and ordering review.

## Related prompts
- [[architecture-decision-record]]
- [[service-boundary-analysis]]

## Tips
- Weight strictness by consumer type — an internal API used by one team can iterate faster than a public contract with unknown consumers.
- Always check idempotency on mutating endpoints; it's the most commonly missed and most costly-to-retrofit property.

## Common mistakes
- Publishing a public API without a versioning mechanism, then being unable to evolve it without breaking every consumer.
- Inconsistent error shapes across endpoints, forcing every client to special-case error handling per endpoint.

## Example
Input: a new public payments API with no idempotency key on the charge-creation endpoint.
Expected output: fails idempotency check, recommends an `Idempotency-Key` header requirement with the specific server-side dedup behavior.

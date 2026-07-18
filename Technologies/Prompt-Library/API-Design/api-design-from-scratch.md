# API Design From Scratch

## Purpose
Design a new API's shape (resources, operations, error model) from
requirements before any contract review happens — this is the design
step; `../Prompt-Library/System-Design/api-contract-design-review.md`
is the review step for a contract that already exists in draft form.

## When to Use
Before writing an OpenAPI spec or route definitions for a brand-new API
surface.

## Expected Inputs
What the API needs to let clients do, who the consumers are (internal,
partner, public), and any existing API conventions in your system to
stay consistent with.

## Expected Outputs
A proposed resource model, the operations on each resource, and an error
model — reasoned from the actual use cases, not a generic REST template.

## Complete Prompt
```
Design this API's shape from the actual use cases — don't default to a
generic CRUD template if the real usage pattern doesn't fit it.

What clients need to do: {the real use cases/user stories}
Consumers: {internal / partner / public}
Existing conventions to match: {naming, auth pattern, pagination style
already used elsewhere in the system, if any}

Design:
1. Resource model — what are the actual nouns, and do they map cleanly
   to REST resources, or does the real use case need an
   action/RPC-style endpoint instead (e.g. "transfer funds" resists being
   forced into a pure resource CRUD shape)? Don't force-fit REST purity
   over what the use case actually needs.
2. Operations per resource — which HTTP methods/endpoints, and for each,
   the request/response shape driven by the actual use case, not every
   theoretically possible field.
3. Error model — a consistent shape (status code + machine-readable code
   + message) across all endpoints, and the specific error cases each
   use case needs to distinguish (not just generic 400/500).
4. Versioning approach appropriate to the consumer type (stricter for
   public/partner APIs than internal).
5. Flag any use case that doesn't fit REST cleanly and would be better
   served by an RPC-style or event-based endpoint instead.
```

## Variations
- **GraphQL variant:** replace resource/endpoint design with schema
  type and resolver design, keeping the same use-case-driven discipline.
- **Internal-service variant:** relax versioning/error-model strictness
  given coordinated deploys are feasible for internal consumers.

## Tips
- Resist forcing every operation into REST purity; a clear action endpoint beats a contorted resource model.
- Design the error model alongside the resource model, not as an afterthought once implementation starts.

## Common Mistakes
- Forcing every use case into REST CRUD even when an action doesn't
  map cleanly to a resource, producing awkward endpoints like `POST
  /accounts/{id}/actions` that obscure intent.
- Designing exhaustive request/response fields "to be safe" instead of
  what the actual use cases need, bloating the contract and confusing
  consumers about what's actually supported.
- Skipping the error model design until implementation, resulting in
  inconsistent error shapes across endpoints built by different people.

## Example Usage
Input: use case = "customer disputes a charge," forcing this into
`PATCH /charges/{id}` obscures the actual semantics. Expected output:
recommends a dedicated `POST /charges/{id}/disputes` action endpoint
instead, with the request shape driven by the actual dispute reason
codes needed, plus the standard error model for validation failures.

## Related Prompts
- [[api-contract-design-review]]
- [[api-versioning-strategy]]
- [[service-boundary-analysis]]

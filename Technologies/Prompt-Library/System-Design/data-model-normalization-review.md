# Data Model Normalization Review

*Purpose:* Review a proposed data model for the right normalization tradeoff given actual query and write patterns, instead of blindly normalizing (or denormalizing) by convention.

## When to use
Before finalizing a database schema for a new feature or service, especially one with both heavy reads and heavy writes.

## Expected input
The proposed schema (tables/entities and relationships), the actual query patterns (reads) and write patterns, and consistency requirements.

## Expected output
A recommendation on where to normalize vs. denormalize, with reasoning tied to the actual access patterns, not a blanket rule.

## The complete prompt
```
Review this data model's normalization tradeoffs against actual access
patterns.

Schema: {tables/entities and relationships}
Read patterns: {the actual queries this needs to serve, and their
frequency}
Write patterns: {what gets written, how often, by what path}
Consistency requirements: {strong consistency needed where, eventual
acceptable where}

For each relationship in the schema:
1. Is it normalized (referenced) or denormalized (duplicated)? State
   which, and whether that matches the read/write frequency — data
   read far more often than written is a denormalization candidate;
   data written frequently with strict consistency needs should usually
   stay normalized.
2. If denormalized, name the specific update-consistency risk (which
   writes must now touch multiple places) and whether that's handled
   (transaction, event-driven sync, accepted staleness window).
3. If normalized but read-heavy with joins, estimate whether the join
   cost at expected scale justifies denormalizing this specific
   relationship.
4. Flag any place where the model normalizes/denormalizes inconsistently
   for no access-pattern reason (arbitrary convention rather than
   deliberate tradeoff).

Give a specific recommendation per relationship, not a general principle.
```

## Variants
- **Analytics/OLAP variant:** bias toward denormalized/star-schema given read-heavy, write-light analytical access patterns.
- **High-write-throughput variant:** bias toward normalized with async denormalized read models (CQRS) instead of synchronous denormalization.

## Related prompts
- [[architecture-decision-record]]
- [[delta-lake-table-design-review]]

## Tips
- Bring actual query and write frequency, not "we read and write this sometimes" — normalization tradeoffs are decided by ratio and consistency needs, not principle alone.
- Check for accidental inconsistency in the model — same kind of relationship normalized in one place, denormalized in another, with no access-pattern justification.

## Example
Input: an order-line-items table normalized (referencing a products table) but read on every order-list page with a join, at high read volume.
Expected output: recommends denormalizing product name/price snapshot onto the order line item at write time, since orders are read far more often than products change, with a note on why price snapshot must not sync back to current product price.

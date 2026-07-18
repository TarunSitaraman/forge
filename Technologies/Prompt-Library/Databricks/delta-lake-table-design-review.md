# Delta Lake Table Design Review

*Purpose:* Review a Delta Lake table's partitioning, schema, and access pattern fit before it accumulates years of data and becomes expensive to restructure.

## When to use
Before creating a new Delta table that will grow large, or when an existing table's queries have gotten slow.

## Expected input
Table schema, current/planned partitioning, typical query patterns, expected growth rate.

## Expected output
A recommendation on partitioning/clustering strategy and schema fixes, with the reasoning tied to the actual query patterns.

## The complete prompt
```
Review this Delta Lake table design against its actual query patterns.

Schema: {columns and types}
Current/planned partitioning: {partition columns}
Typical queries: {the actual WHERE clauses / access patterns used against
this table}
Expected growth: {rows/day or GB/day}

Evaluate:
1. Partition column fit — does the partition column appear as a filter in
   most real queries? If not, name a better candidate or recommend
   Z-ORDER/liquid clustering instead of partitioning.
2. Partition cardinality — will this partitioning scheme create too many
   small partitions (over-partitioning) or too few large ones
   (under-partitioning) at the expected growth rate?
3. Schema evolution risk — any column likely to need a type change or
   nested-structure change, and is schema evolution (mergeSchema) safely
   handled for it?
4. File size / compaction — at this growth rate, will small-file
   accumulation become a problem, and what OPTIMIZE/auto-compaction
   schedule is appropriate?
5. Time travel / retention — is the VACUUM retention period set
   appropriately for this table's audit/rollback needs vs. storage cost?

Give a concrete recommendation for partitioning/clustering, not just a
list of considerations.
```

## Variants
- **Streaming ingestion variant:** add explicit late-arriving-data and watermark handling for the partition scheme.
- **Multi-tenant variant:** evaluate tenant_id as a partition/clustering candidate explicitly against query isolation needs.

## Related prompts
- [[databricks-job-cost-perf-review]]
- [[architecture-decision-record]]

## Tips
- Bring real query examples, not a description of "we query by date sometimes" — the actual WHERE clauses determine the right partition column.
- Reconsider partitioning by high-cardinality columns (e.g. user_id) — Z-ORDER/liquid clustering is usually the better fit.

## Common mistakes
- Partitioning by a column that queries rarely filter on, forcing full-table scans anyway.
- Over-partitioning by a fine-grained timestamp, producing thousands of tiny files that hurt both storage and query planning.

## Example
Input: a table partitioned by `event_timestamp` (to the second) but queries always filter by `date` and `customer_id`.
Expected output: recommends partitioning by `date` only, with Z-ORDER on `customer_id`, and a compaction schedule given the stated ingestion rate.

# Databricks Job Cost & Performance Review

*Purpose:* Diagnose why a Databricks job is slow or expensive using cluster config and Spark UI evidence, not generic "use more nodes" advice.

## When to use
A recurring job's runtime or DBU cost has grown, or a new job needs review before it's scheduled to run daily.

## Expected input
Job/cluster config (cluster type, autoscaling settings, instance types), the Spark job's stage/task metrics (from Spark UI or job run details), and data volume.

## Expected output
A ranked list of specific bottlenecks with the exact config or code change to fix each.

## The complete prompt
```
Diagnose this Databricks job's cost/performance issue using the evidence
given — don't guess generically.

Cluster config: {cluster type, node count/autoscale range, instance type,
photon on/off}
Spark UI metrics: {stage durations, shuffle read/write, spill to disk,
task skew}
Data volume: {input size, partition count}
Job frequency/cost: {DBU cost, runtime}

Diagnose in this order:
1. Data skew — is task duration wildly uneven across partitions? Name the
   stage and the likely skewed key.
2. Spill to disk — any stage spilling? That points to under-provisioned
   executor memory or too few partitions for the data volume.
3. Shuffle volume — is a join/groupBy producing more shuffle than the data
   volume justifies? Suggest broadcast join or repartitioning if so.
4. Cluster right-sizing — is the cluster idle-waiting (over-provisioned)
   or queueing (under-provisioned) based on the metrics given?
5. Photon/runtime — would Photon acceleration or a newer DBR version plausibly
   help this specific workload (e.g. heavy SQL/joins benefit more than UDF-heavy jobs)?

For each diagnosis: give the specific fix (repartition count, broadcast
hint, cluster config change, code change) — not "tune your cluster."
```

## Variants
- **Streaming job variant:** replace shuffle analysis with watermark/trigger-interval and backpressure analysis.
- **Delta Lake variant:** add explicit small-file/compaction (OPTIMIZE/Z-ORDER) analysis.

## Related prompts
- [[cloud-cost-audit]]
- [[scalability-stress-test-review]]

## Tips
- Pull the actual Spark UI stage metrics before running this — diagnosis without them is guesswork.
- Data skew is the single most common cause of "add more nodes doesn't help" — check it first.

## Common mistakes
- Scaling up cluster size to fix a skew problem, which doesn't help since one task still bottlenecks the stage.
- Ignoring shuffle volume and jumping straight to hardware changes when a broadcast join would eliminate the shuffle entirely.

## Example
Input: a daily ETL job with one stage taking 10x longer than others, heavy shuffle write, no spill.
Expected output: diagnoses likely key skew in a groupBy, recommends salting the skewed key or switching to a two-stage aggregation.

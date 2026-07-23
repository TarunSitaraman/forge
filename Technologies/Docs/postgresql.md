# PostgreSQL

*One authoritative reference. This is not a note collection — if you
learn something new about PostgreSQL worth keeping, it gets merged into
the relevant section below, not appended as a new file.*

## Overview

PostgreSQL is an open-source, object-relational database that pairs
strict transactional guarantees (full ACID compliance via MVCC) with an
extensible type system — JSON/JSONB, arrays, ranges, full-text search,
and extensions like `pgvector` for embeddings and `PostGIS` for
geospatial data. It's the default choice for systems that need real
relational integrity (foreign keys, constraints, transactions) but don't
want to give up document-style flexibility for the columns that need it.

Core objects: **databases** (isolated namespaces on one server),
**schemas** (namespaces within a database, default `public`), **tables**
(rows conforming to a column definition), **indexes** (structures that
speed lookups at the cost of write overhead and storage), and
**extensions** (opt-in modules like `pgvector`, `PostGIS`, `pg_trgm`
loaded via `CREATE EXTENSION`).

## Mental model

Every row modification in PostgreSQL creates a new row version rather
than overwriting in place — this is **MVCC** (Multi-Version Concurrency
Control). Readers never block writers and writers never block readers,
because each transaction sees a consistent snapshot of the data as of
when it started, regardless of concurrent writes landing after that
point. The cost: dead row versions accumulate and must be reclaimed by
**VACUUM**, and a table under heavy update/delete churn without regular
vacuuming bloats and slows down.

Think of a table's data as an append-only log of row versions, with the
visible "current" row being whichever version is still valid for your
transaction's snapshot. This is why `UPDATE` in Postgres is really
"insert a new version, mark the old one dead" — not an in-place mutation
— and why long-running transactions are dangerous: they hold back the
oldest snapshot Postgres must keep every dead version alive for,
preventing vacuum from reclaiming space.

## Architecture

```mermaid
flowchart TD
    subgraph Client
        APP[Application] -->|SQL over libpq| CONN[Connection]
    end
    subgraph PostgreSQL Server
        CONN --> BACKEND[Backend process per connection]
        BACKEND --> SHMEM[(Shared Buffers - cache)]
        BACKEND --> WAL[WAL - Write-Ahead Log]
        WAL --> DISK[(Heap + Index files on disk)]
        SHMEM --> DISK
        VAC[Autovacuum worker] --> DISK
    end
    WAL -- streaming replication --> REPLICA[(Read Replica)]
```

**Process model:** Postgres forks one OS process per client connection
(not a thread), which is why connection count matters — each connection
carries real memory overhead, and this is why connection poolers
(PgBouncer, Supabase's pooler, RDS Proxy) sit in front of Postgres in
production rather than letting application instances connect directly.

**Durability:** every write goes to the **WAL** (Write-Ahead Log) before
the corresponding data page is modified on disk — if the server crashes
mid-write, WAL replay on restart brings the database back to a
consistent state. Streaming replication ships the WAL to replicas in
near-real-time, which is also how point-in-time recovery works: replay
WAL up to a specific timestamp.

## Common workflows

**Connecting and basic inspection**
```bash
psql "postgresql://user:pass@host:5432/dbname"
\dt                  # list tables
\d tablename         # describe table (columns, indexes, constraints)
\di                  # list indexes
```

**Transactions**
```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;  -- or ROLLBACK on error
```

**Indexing for a common query pattern**
```sql
-- Speeds up WHERE patient_id = ? AND created_at > ?
CREATE INDEX idx_vitals_patient_time ON vitals (patient_id, created_at);

-- Partial index: only indexes rows matching the predicate — smaller,
-- faster, when queries always filter on that predicate too
CREATE INDEX idx_active_patients ON patients (id) WHERE status = 'active';
```

**Diagnosing a slow query**
```sql
EXPLAIN ANALYZE
SELECT * FROM vitals WHERE patient_id = 'p-1' AND created_at > now() - interval '1 hour';
-- Look for "Seq Scan" on a large table (missing index) vs "Index Scan"
```

**JSONB for semi-structured columns**
```sql
ALTER TABLE events ADD COLUMN metadata JSONB;
CREATE INDEX idx_events_metadata ON events USING GIN (metadata);
SELECT * FROM events WHERE metadata @> '{"source": "wearable"}';
```

**pgvector for embeddings (RAG/AI workloads)**
```sql
CREATE EXTENSION IF NOT EXISTS vector;
CREATE TABLE documents (id bigserial PRIMARY KEY, embedding vector(1536));
CREATE INDEX ON documents USING hnsw (embedding vector_cosine_ops);
SELECT id FROM documents ORDER BY embedding <=> '[0.1, 0.2, ...]' LIMIT 5;
```

## Common mistakes

- **Not indexing foreign key columns.** Postgres does not automatically
  index foreign keys (unlike the primary key side); every join or
  cascade delete on that column does a sequential scan without one.
- **Long-running transactions holding back vacuum.** An open transaction
  — even an idle one left open by a leaked connection — prevents
  Postgres from reclaiming dead row versions older than its snapshot,
  causing table bloat across the whole database, not just the tables
  that transaction touched.
- **`SELECT *` in application code against wide tables.** Pulls
  unnecessary columns over the wire and defeats index-only scans that
  could otherwise avoid touching the heap at all.
- **Using `SERIAL`/auto-increment primary keys in a system that needs
  to merge data from multiple sources** (multi-region writes, offline
  sync) — IDs collide. Use `UUID` or a `bigint` id generator (Snowflake-
  style) when that's a real requirement.
- **N+1 queries from an ORM** — fetching a list, then querying per-row
  for related data instead of a single join or `IN (...)` batch fetch.
- **Not setting `statement_timeout`** in application connection config,
  letting one runaway query hold a connection (and locks) indefinitely.
- **Confusing `VARCHAR(n)` with performance.** Postgres stores `text`
  and `varchar` identically internally; the length constraint is purely
  a validation check, not a storage or speed optimization.

## Best practices

- Always index foreign key columns explicitly.
- Use connection pooling (PgBouncer, Supabase pooler) in front of
  Postgres for any application with more than a handful of concurrent
  instances — raw connections don't scale past a few hundred.
- Wrap multi-statement writes that must succeed or fail together in an
  explicit transaction — don't rely on autocommit-per-statement leaving
  the database in a partially-updated state on failure.
- Use `EXPLAIN ANALYZE` before assuming an index is needed — reason from
  the actual query plan, not intuition.
- Set up `pg_stat_statements` to find your slowest/most frequent queries
  in production rather than guessing.
- For encrypted-at-rest sensitive columns (PII, health data), encrypt at
  the application layer before writing `BYTEA`/`TEXT` — Postgres's
  disk-level encryption (if enabled) protects against stolen disks, not
  against a compromised database credential.
- Run `VACUUM` (or trust autovacuum, tuned for write-heavy tables) and
  monitor table/index bloat on high-churn tables.

## Cheatsheet

| Task | Command |
|---|---|
| Connect | `psql "postgresql://user:pass@host/db"` |
| List tables | `\dt` |
| Describe table | `\d tablename` |
| List indexes | `\di` |
| Show query plan | `EXPLAIN ANALYZE <query>` |
| Create index | `CREATE INDEX idx_name ON table (col);` |
| Create unique constraint | `ALTER TABLE t ADD CONSTRAINT c UNIQUE (col);` |
| Dump database | `pg_dump dbname > backup.sql` |
| Restore database | `psql dbname < backup.sql` |
| Kill a stuck query | `SELECT pg_terminate_backend(pid);` |
| Current connections | `SELECT * FROM pg_stat_activity;` |
| Table size | `SELECT pg_size_pretty(pg_total_relation_size('t'));` |

## Interview questions

1. What is MVCC and why does Postgres use it instead of locking readers
   and writers against each other?
   *(Multi-Version Concurrency Control — each transaction sees a
   snapshot as of its start time via row versioning, so reads never
   block writes and vice versa; the tradeoff is dead row versions that
   VACUUM must reclaim.)*
2. Why can a long-idle transaction cause performance problems even if
   it isn't running any queries?
   *(It holds the oldest snapshot Postgres must preserve dead row
   versions for, blocking vacuum from reclaiming space — table bloat
   grows across the database, not just tables the idle transaction
   touched.)*
3. When would you choose a JSONB column over a normalized table?
   *(Semi-structured or sparse/variable-shape data that's read as a
   whole and doesn't need relational joins/constraints on its internal
   fields — e.g. event metadata, feature flags. GIN-indexed JSONB
   supports containment queries, but it forfeits foreign keys and typed
   column constraints on that data.)*
4. What's the difference between a b-tree index and a partial index,
   and when would you use the latter?
   *(A b-tree indexes every row; a partial index only indexes rows
   matching a `WHERE` predicate — smaller and faster when queries
   always filter on that same predicate, e.g. `WHERE status = 'active'`
   on a mostly-inactive table.)*
5. Why does Postgres fork a process per connection, and what's the
   practical consequence?
   *(Each connection is a full OS process with real memory overhead —
   past a few hundred concurrent connections this becomes expensive,
   which is why production deployments front Postgres with a connection
   pooler like PgBouncer rather than connecting directly per app
   instance.)*

## Useful links

- [Official PostgreSQL documentation](https://www.postgresql.org/docs/)
- [pgvector extension](https://github.com/pgvector/pgvector)
- [Use the Index, Luke — indexing guide](https://use-the-index-luke.com/)

## Further reading

- "The Internals of PostgreSQL" (Hironobu Suzuki) — free online, the
  deepest source for how MVCC, WAL, and the planner actually work.
- `EXPLAIN (ANALYZE, BUFFERS)` output interpretation — worth mastering
  once query performance actually matters in production.

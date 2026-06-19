# PostgreSQL Glossary

Canonical terminology for this teaching workspace.

## Architecture

**MVCC (Multi-Version Concurrency Control)**:
PostgreSQL's mechanism for handling concurrent transactions without locking readers or writers. Each transaction sees a snapshot of the database as of when it started. Old row versions (dead tuples) are cleaned up by VACUUM.

**WAL (Write-Ahead Log)**:
The transaction log. Every change is written to WAL before it's applied to data files. Enables crash recovery, point-in-time recovery, and replication.

**TOAST (The Oversized-Attribute Storage Technique)**:
PostgreSQL's mechanism for storing large values (>2KB) in separate overflow tables. Most columns never trigger TOAST, but JSONB and TEXT values can.

**Shared Buffers**:
PostgreSQL's in-memory cache for data pages. Set to ~25% of RAM. When a query needs data, PostgreSQL checks shared_buffers first, then disk.

## Concurrency

**Snapshot Isolation**:
Each transaction sees a consistent snapshot of committed data. "REPEATABLE READ" freezes this snapshot for the entire transaction. "READ COMMITTED" refreshes it per query.

**xmin / xmax**:
Hidden system columns on every row. `xmin` is the transaction ID that created this row version. `xmax` is the transaction ID that deleted/obsoleted it (0 if still live).

**Dead Tuple**:
An obsolete row version created by an UPDATE or DELETE. Visible to no active transaction. VACUUM reclaims the space.

**Transaction ID Wraparound**:
When a 32-bit transaction ID counter wraps past 2 billion. Rows with old xmin values become invisible. Autovacuum prevents this by freezing old rows.

## Indexing

**B-tree**:
Default index type. Balanced tree for equality and range queries. Supports sorting. Best all-purpose index.

**GiST (Generalized Search Tree)**:
Index for full-text search, geometry, ranges, and custom data types. Balances query speed and build time.

**GIN (Generalized Inverted Index)**:
Index for JSONB, arrays, and full-text tsvector. Fast to query, slower to build. Use `jsonb_path_ops` for JSONB.

**BRIN (Block Range Index)**:
Index for huge, append-only tables where data is physically ordered. Tiny compared to B-tree, only useful for range queries.

**Partial Index**:
An index with a WHERE clause. Only indexes matching rows. Smaller and faster than a full index.

**Covering Index / INCLUDE**:
An index that stores extra non-key columns to enable Index Only Scans.

**Index Only Scan**:
A query that reads all needed data from the index alone, without visiting the heap (table). Fastest scan type.

## Query Planning

**Seq Scan**:
Full sequential table scan. PostgreSQL reads every page. Efficient for small tables or when reading >5-10% of rows.

**Index Scan**:
B-tree lookup followed by heap fetch. Efficient for selective queries.

**Bitmap Index Scan + Bitmap Heap Scan**:
Two-phase scan: builds a bitmap of matching pages from the index, then fetches those pages. Used when the planner expects many matching rows but not enough for a Seq Scan.

**Nested Loop**:
For each row in the outer input, scan the inner input once. Best when outer is small and inner has an index.

**Hash Join**:
Build a hash table from the inner input, probe with each outer row. Best for medium-sized, unsorted tables.

**Merge Join**:
Walk both sorted inputs in parallel. Best for large, pre-sorted datasets (e.g., from indexes).

## Maintenance

**VACUUM**:
Marks dead tuple space as reusable. Does not shrink file size. Runs automatically via autovacuum.

**VACUUM FULL**:
Rewrites the entire table to compact disk space. Takes ACCESS EXCLUSIVE lock (table unavailable). For maintenance windows only.

**Autovacuum**:
PostgreSQL's automatic VACUUM daemon. Triggered by dead tuple thresholds. Settings: `scale_factor`, `threshold`, `naptime`, `max_workers`.

**ANALYZE**:
Updates table statistics for the query planner. Without fresh stats, the planner makes bad row estimates. Run after significant data changes.

**pg_stat_statements**:
An extension that tracks query execution statistics (calls, total time, rows, I/O). The primary tool for identifying slow queries.

## Configuration

**work_mem**:
Per-operation memory for sorts, hash tables, and joins. Each sort operation gets its own allocation. Too high → OOM. Too low → disk spills.

**effective_cache_size**:
Planner's estimate of available OS-level cache. Does not allocate memory — tells the planner how likely data is to be cached.

**random_page_cost**:
Cost of a random disk page read. Default 4.0 (HDD). Set to 1.1 for SSD. Significantly affects index vs seq scan choices.

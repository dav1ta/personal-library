# PostgreSQL Performance (Practical)

## 1) Find the Real Bottleneck
```sql
EXPLAIN (ANALYZE, BUFFERS) SELECT ...;
```
Look for sequential scans, high rows vs. estimates, and heavy disk reads.

## 2) Indexing Basics
```sql
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_active_users ON users(email) WHERE active = true;
```
- Use partial indexes for hot subsets.
- Use GIN for JSONB / arrays; GiST for geometry.

## 3) Query Shape
- Avoid `SELECT *` on hot paths.
- Add `LIMIT` with `ORDER BY` and index the sort column.
- Use `EXISTS` for existence checks.

## 4) N+1 and Joins
- Prefer a single join over many tiny queries.
- For ORM workloads, prefetch in batches.

## 5) Connection Pooling
Use PgBouncer for many concurrent clients; keep `max_connections` low.

## 6) Maintenance
```sql
VACUUM (ANALYZE) your_table;
REINDEX INDEX idx_users_email;
```

## 7) Observability
Enable `pg_stat_statements` and log slow queries.

## Next Steps
- [Configuration](../basics/configuration.md)

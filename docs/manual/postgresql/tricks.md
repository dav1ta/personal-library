# PostgreSQL Tips (No Myths)

Short, accurate advice that actually helps in production.

## Query Tuning Checklist
- Use `EXPLAIN (ANALYZE, BUFFERS)` before changing anything.
- Index the columns used in `WHERE`, `JOIN`, and `ORDER BY`.
- Don't force "fast" patterns; the planner is usually right when stats are fresh.
- Avoid huge `IN (...)` lists; use temp tables or joins.
- Prefer `EXISTS` for existence checks.

## Indexing Tips
- Use partial indexes for hot subsets.
- Use `GIN` for JSONB/arrays.
- Drop unused indexes; they slow down writes.

## Data Access
- Use `COPY` for bulk loads.
- Batch writes; avoid chatty single-row inserts.

## Maintenance
- Keep autovacuum healthy.
- Monitor bloat and long-running transactions.

## Realistic SQL Examples
```sql
-- Existence check
SELECT 1 FROM orders WHERE user_id = $1 LIMIT 1;

-- Partial index
CREATE INDEX idx_orders_open ON orders(created_at) WHERE status = 'open';

-- JSONB index
CREATE INDEX idx_meta ON events USING gin (meta);
```

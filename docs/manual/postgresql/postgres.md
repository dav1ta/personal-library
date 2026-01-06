# PostgreSQL Advanced Notes

Focused notes for production use. Use the official docs for full coverage.

## Data Types and Extensions
- JSONB for semi-structured data; index with GIN.
- Arrays are fine for small sets; avoid unbounded growth.
- Common extensions: `pg_stat_statements`, `uuid-ossp`, `pgcrypto`, `postgis`.

## Partitioning
- Partition for very large tables or time-based retention.
- Keep partitions sized so vacuum/analyze stays fast.

## Replication / HA
- Streaming replication for read replicas.
- Use a failover manager (Patroni or managed service).

## Backup / Restore
- Use `pg_dump` for logical backups.
- Use `pg_basebackup` or snapshots for full cluster backups.
- Test restores regularly.

## Security
- Least-privilege roles.
- Use SSL/TLS.
- Restrict networks in `pg_hba.conf`.

## Monitoring
- Enable `pg_stat_statements`.
- Track slow queries and lock contention.
- Watch autovacuum and long transactions.

## Related Docs
- [Configuration](basics/configuration.md)
- [Performance](advanced/performance.md)

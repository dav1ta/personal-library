# PostgreSQL Configuration (Essentials)

Keep config changes minimal and measurable. Start with defaults and tune based on real load.

## Key Files
- `postgresql.conf` (server settings)
- `pg_hba.conf` (client auth rules)

## Core Settings to Know
```conf
max_connections = 100          # keep low; use pooling
shared_buffers = 25%RAM        # rule of thumb
work_mem = 4-32MB              # per sort/hash op
maintenance_work_mem = 256MB   # vacuum/index build
log_min_duration_statement = 500
```

## Connection Pooling
For high concurrency, use PgBouncer or Pgpool instead of raising `max_connections`.

## WAL / Checkpoints
```conf
wal_level = replica
checkpoint_timeout = 5min
max_wal_size = 1GB
```

## Autovacuum (Don't Disable)
```conf
autovacuum = on
autovacuum_vacuum_scale_factor = 0.2
autovacuum_analyze_scale_factor = 0.1
```

## Best Practices
- Change one thing at a time and measure.
- Document every change and why it was made.
- Keep config in version control.

## Next Steps
- [Performance](../advanced/performance.md)

Next: [Performance](../advanced/performance.md)

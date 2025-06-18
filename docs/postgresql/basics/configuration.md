# PostgreSQL Configuration Guide

This guide covers the essential configuration settings for PostgreSQL to optimize performance and security.

## Table of Contents
- [Configuration Files](#configuration-files)
- [Memory Settings](#memory-settings)
- [Connection Settings](#connection-settings)
- [Write-Ahead Log (WAL)](#write-ahead-log-wal)
- [Query Planning](#query-planning)
- [Logging](#logging)
- [Autovacuum](#autovacuum)

## Configuration Files

### Main Configuration File
Location: `postgresql.conf`
- Linux: `/etc/postgresql/<version>/main/postgresql.conf`
- macOS: `/usr/local/var/postgres/postgresql.conf`
- Windows: `C:\Program Files\PostgreSQL\<version>\data\postgresql.conf`

### Client Authentication
Location: `pg_hba.conf`
- Contains client authentication rules
- Controls which hosts can connect to which databases

## Memory Settings

### Shared Buffers
```conf
# Typically 25% of system RAM
shared_buffers = 2GB
```
- Main memory area for caching data
- Affects query performance
- Too large can cause memory pressure

### Work Memory
```conf
# Memory for sorting and joins
work_mem = 64MB
```
- Used for sorting operations and hash tables
- Per-operation setting
- Adjust based on concurrent operations

### Maintenance Work Memory
```conf
# Memory for maintenance operations
maintenance_work_mem = 1GB
```
- Used for VACUUM, CREATE INDEX, etc.
- Larger values speed up maintenance operations

### Effective Cache Size
```conf
# Estimate of available system memory
effective_cache_size = 6GB
```
- Helps query planner make better decisions
- Should be set to about 75% of system RAM

## Connection Settings

### Max Connections
```conf
# Maximum number of concurrent connections
max_connections = 100
```
- Each connection consumes memory
- Adjust based on application needs
- Consider connection pooling for high concurrency

### Connection Timeout
```conf
# Connection timeout in seconds
statement_timeout = 60000
```
- Prevents long-running queries
- Helps manage resource usage

## Write-Ahead Log (WAL)

### WAL Level
```conf
# WAL level
wal_level = replica
```
Options:
- `minimal`: Basic crash recovery
- `replica`: Supports replication
- `logical`: Supports logical replication

### Checkpoint Settings
```conf
# Checkpoint frequency
checkpoint_timeout = 5min
max_wal_size = 1GB
```
- Controls how often checkpoints occur
- Affects recovery time and I/O load

## Query Planning

### Random Page Cost
```conf
# Cost of random page access
random_page_cost = 1.1
```
- Lower for SSD storage
- Higher for HDD storage
- Affects query plan choices

### CPU Cost Parameters
```conf
# CPU cost parameters
cpu_tuple_cost = 0.01
cpu_index_tuple_cost = 0.005
cpu_operator_cost = 0.0025
```
- Fine-tune query planner behavior
- Adjust based on your hardware

## Logging

### Basic Logging
```conf
# Enable logging
logging_collector = on
log_directory = 'log'
log_filename = 'postgresql-%Y-%m-%d_%H%M%S.log'
```

### Query Logging
```conf
# Log slow queries
log_min_duration_statement = 1000  # milliseconds

# Log all queries (development only)
log_statement = 'all'
```

## Autovacuum

### Basic Settings
```conf
# Enable autovacuum
autovacuum = on

# Autovacuum parameters
autovacuum_vacuum_scale_factor = 0.2
autovacuum_analyze_scale_factor = 0.1
autovacuum_vacuum_threshold = 50
autovacuum_analyze_threshold = 50
```

### Table-Specific Settings
```sql
-- Set autovacuum parameters for specific table
ALTER TABLE my_table SET (
    autovacuum_vacuum_scale_factor = 0.05,
    autovacuum_analyze_scale_factor = 0.02
);
```

## Best Practices

1. **Start Conservative**
   - Begin with default settings
   - Monitor performance
   - Adjust gradually

2. **Regular Monitoring**
   - Use `pg_stat_statements`
   - Monitor system resources
   - Check query performance

3. **Security First**
   - Restrict network access
   - Use strong passwords
   - Regular security audits

4. **Backup Configuration**
   - Keep configuration backups
   - Document changes
   - Test changes in staging

- [Performance Tuning](../advanced/performance.md)
- Monitoring and Maintenance
- Security Best Practices 
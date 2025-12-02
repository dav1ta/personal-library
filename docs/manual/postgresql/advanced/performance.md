# PostgreSQL Performance Optimization Guide

This guide covers advanced techniques for optimizing PostgreSQL performance, from query optimization to system-level tuning.

## Table of Contents
- [Query Optimization](#query-optimization)
- [Indexing Strategies](#indexing-strategies)
- [Connection Optimization](#connection-optimization)
- [Storage Optimization](#storage-optimization)
- [Monitoring and Analysis](#monitoring-and-analysis)
- [Common Performance Issues](#common-performance-issues)

## Query Optimization

### Understanding Query Plans
```sql
-- Get query plan
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'user@example.com';

-- Get detailed query plan
EXPLAIN (ANALYZE, BUFFERS, FORMAT JSON) 
SELECT * FROM users WHERE email = 'user@example.com';
```

### Common Query Optimizations

1. **Use Indexes Effectively**
```sql
-- Create index for frequently queried columns
CREATE INDEX idx_users_email ON users(email);

-- Create partial index for specific conditions
CREATE INDEX idx_active_users ON users(email) WHERE status = 'active';
```

2. **Avoid SELECT ***
```sql
-- Instead of
SELECT * FROM users;

-- Use specific columns
SELECT id, email, name FROM users;
```

3. **Use LIMIT with ORDER BY**
```sql
-- Add index for the sort
CREATE INDEX idx_users_created_at ON users(created_at);

-- Use limit with order by
SELECT * FROM users ORDER BY created_at DESC LIMIT 10;
```

## Indexing Strategies

### Types of Indexes

1. **B-tree Index (Default)**
```sql
CREATE INDEX idx_users_name ON users(name);
```
- Best for equality and range queries
- Default index type
- Good for most use cases

2. **Hash Index**
```sql
CREATE INDEX idx_users_email_hash ON users USING hash(email);
```
- Best for equality comparisons
- Smaller than B-tree
- No support for range queries

3. **GiST Index**
```sql
CREATE INDEX idx_geometries ON geometries USING gist(geom);
```
- For geometric data
- Full-text search
- Custom data types

4. **GIN Index**
```sql
CREATE INDEX idx_users_tags ON users USING gin(tags);
```
- For array values
- Full-text search
- JSON/JSONB data

### Index Maintenance
```sql
-- Rebuild index
REINDEX INDEX idx_users_name;

-- Analyze table
ANALYZE users;

-- Vacuum analyze
VACUUM ANALYZE users;
```

## Connection Optimization

### Connection Pooling
```bash
# Install pgBouncer
sudo apt install pgbouncer

# Configure pgBouncer
# /etc/pgbouncer/pgbouncer.ini
[databases]
mydb = host=127.0.0.1 port=5432 dbname=mydb

[pgbouncer]
pool_mode = transaction
max_client_conn = 1000
default_pool_size = 20
```

### Connection Settings
```conf
# postgresql.conf
max_connections = 100
superuser_reserved_connections = 3
```

## Storage Optimization

### Table Partitioning
```sql
-- Create partitioned table
CREATE TABLE orders (
    id SERIAL,
    order_date DATE,
    customer_id INTEGER,
    amount DECIMAL
) PARTITION BY RANGE (order_date);

-- Create partitions
CREATE TABLE orders_2023_q1 PARTITION OF orders
    FOR VALUES FROM ('2023-01-01') TO ('2023-04-01');
```

### TOAST Tables
- Large values are automatically compressed
- Stored in separate TOAST tables
- Consider column storage strategy:
```sql
ALTER TABLE my_table ALTER COLUMN large_text SET STORAGE EXTENDED;
```

## Monitoring and Analysis

### Performance Views
```sql
-- Check table statistics
SELECT * FROM pg_stat_user_tables;

-- Check index usage
SELECT * FROM pg_stat_user_indexes;

-- Check query statistics
SELECT * FROM pg_stat_statements;
```

### Logging Slow Queries
```conf
# postgresql.conf
log_min_duration_statement = 1000  # milliseconds
```

## Common Performance Issues

### 1. Slow Queries
- Use EXPLAIN ANALYZE
- Check index usage
- Optimize query structure

### 2. High I/O
- Increase shared_buffers
- Use SSDs
- Implement connection pooling

### 3. Memory Pressure
- Adjust work_mem
- Monitor connection count
- Use connection pooling

### 4. Lock Contention
```sql
-- Check for locks
SELECT * FROM pg_locks;

-- Check for blocked queries
SELECT blocked_locks.pid AS blocked_pid,
       blocked_activity.usename AS blocked_user,
       blocking_locks.pid AS blocking_pid,
       blocking_activity.usename AS blocking_user
FROM pg_catalog.pg_locks blocked_locks
JOIN pg_catalog.pg_stat_activity blocked_activity ON blocked_activity.pid = blocked_locks.pid
JOIN pg_catalog.pg_locks blocking_locks 
    ON blocking_locks.locktype = blocked_locks.locktype
    AND blocking_locks.DATABASE IS NOT DISTINCT FROM blocked_locks.DATABASE
    AND blocking_locks.relation IS NOT DISTINCT FROM blocked_locks.relation
    AND blocking_locks.page IS NOT DISTINCT FROM blocked_locks.page
    AND blocking_locks.tuple IS NOT DISTINCT FROM blocked_locks.tuple
    AND blocking_locks.virtualxid IS NOT DISTINCT FROM blocked_locks.virtualxid
    AND blocking_locks.transactionid IS NOT DISTINCT FROM blocked_locks.transactionid
    AND blocking_locks.classid IS NOT DISTINCT FROM blocked_locks.classid
    AND blocking_locks.objid IS NOT DISTINCT FROM blocked_locks.objid
    AND blocking_locks.objsubid IS NOT DISTINCT FROM blocked_locks.objsubid
    AND blocking_locks.pid != blocked_locks.pid
JOIN pg_catalog.pg_stat_activity blocking_activity ON blocking_activity.pid = blocking_locks.pid
WHERE NOT blocked_locks.GRANTED;
```

## Best Practices

1. **Regular Maintenance**
   - Run VACUUM regularly
   - Update statistics with ANALYZE
   - Monitor index usage

2. **Query Design**
   - Use appropriate indexes
   - Avoid SELECT *
   - Use LIMIT with ORDER BY

3. **Configuration**
   - Start with conservative settings
   - Monitor and adjust gradually
   - Document changes

4. **Monitoring**
   - Set up regular monitoring
   - Track slow queries
   - Monitor system resources

- [Configuration Guide](../basics/configuration.md)
- Monitoring and Maintenance
- [Indexing Strategies](#indexing-strategies) 
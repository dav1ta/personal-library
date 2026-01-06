PostgreSQL Lab — real problems and one‑file solutions

Each solution is a single runnable Python file under `postgres-lab/src/`.
Set `DATABASE_URL=postgresql://user:pass@host:5432/db` before running.

How to get newer PostgreSQL quickly
- Docker (easiest):
  - PG16: `docker run --rm -e POSTGRES_PASSWORD=pg -p 5432:5432 postgres:16`
  - PG17: `docker run --rm -e POSTGRES_PASSWORD=pg -p 5432:5432 postgres:17`
  - PG18 (when available): `docker run --rm -e POSTGRES_PASSWORD=pg -p 5432:5432 postgres:18`
- Local packages: install the desired major version from your OS repo or PGDG packages.
- Verify: `SHOW server_version, server_version_num;`
- Python driver: `pip install psycopg[binary]`

1) Bulk ingest is too slow with many INSERTs
- How we solved it
  Use `COPY FROM STDIN` to stream rows in one statement; batch commits and keep transactions short. Prefer COPY for large payloads over INSERT loops.
- File
  `postgres-lab/src/1_copy_ingest.py`
- Code
  ```python
  import os, psycopg
  dsn = os.environ["DATABASE_URL"]
  with psycopg.connect(dsn) as conn, conn.cursor() as cur:
      cur.execute("drop table if exists demo_copy")
      cur.execute("create table demo_copy(k int, v text)")
      sql = psycopg.sql.SQL("COPY {} (k,v) FROM STDIN").format(psycopg.sql.Identifier("demo_copy"))
      with cur.copy(sql) as cp:
          for i in range(1000):
              cp.write(f"{i}\tval_{i}\n")
      conn.commit()
  ```

2) Many small inserts are latency‑bound
- How we solved it
  Use libpq Pipeline mode (14+) to queue statements and reduce round‑trips; choose moderate batch sizes (hundreds–thousands) and commit periodically.
- File
  `postgres-lab/src/2_pipeline_inserts.py`
- Code
  ```python
  import os, psycopg
  dsn = os.environ["DATABASE_URL"]
  with psycopg.connect(dsn) as conn:
      with conn.cursor() as cur:
          cur.execute("drop table if exists demo_pipe")
          cur.execute("create table demo_pipe(id int primary key, note text)")
      sql = "insert into demo_pipe(id,note) values (%s,%s)"
      if hasattr(conn, "pipeline"):
          with conn.pipeline() as p:
              with p.cursor() as cur:
                  for i in range(1000):
                      cur.execute(sql, (i, f"n{i}"))
      else:
          with conn.cursor() as cur:
              cur.executemany(sql, [(i, f"n{i}") for i in range(1000)])
      conn.commit()
  ```

3) Portable UPSERT across versions
- How we solved it
  Detect features at runtime: prefer `MERGE` (15+) and `MERGE ... RETURNING` (17+), else fallback to `INSERT ... ON CONFLICT DO UPDATE`. Keep writes idempotent.
- File
  `postgres-lab/src/3_upsert_portable.py`
- Code
  ```python
  import os, psycopg
  dsn = os.environ["DATABASE_URL"]
  with psycopg.connect(dsn) as conn, conn.cursor() as cur:
      cur.execute("drop table if exists demo_upsert")
      cur.execute("create table demo_upsert(k text primary key, v int)")
      cur.execute("show server_version_num"); v=int(cur.fetchone()[0]); has_merge=(v//10000)>=15
      rows = [("a",1),("a",2)]
      if has_merge:
          cur.execute("MERGE INTO demo_upsert t USING (values (%s,%s),(%s,%s)) s(k,v) ON t.k=s.k WHEN MATCHED THEN UPDATE SET v=s.v WHEN NOT MATCHED THEN INSERT (k,v) VALUES (s.k,s.v)", (rows[0][0],rows[0][1],rows[1][0],rows[1][1]))
      else:
          cur.executemany("insert into demo_upsert(k,v) values (%s,%s) on conflict (k) do update set v=excluded.v", rows)
      conn.commit()
  ```

4) Push JSON filtering to SQL
- How we solved it
  Use SQL/JSONPath (12+) to extract and filter server‑side; index hot paths with GIN as needed. Reduce payload transfer and Python CPU.
- File
  `postgres-lab/src/4_jsonpath_filter.py`
- Code
  ```python
  import os, psycopg
  doc = {"users": [{"id":1,"role":"admin"},{"id":2,"role":"user"}]}
  expr = "$.users[*] ? (@.role == 'user').id"
  with psycopg.connect(os.environ["DATABASE_URL"]) as conn, conn.cursor() as cur:
      cur.execute("select jsonb_path_query(%s::jsonb,%s)", (psycopg.adapters.Json(doc), expr))
      print([r[0] for r in cur.fetchall()])
  ```

5) Observe I/O with pg_stat_io (16+)
- How we solved it
  Query `pg_stat_io` to see read/write patterns and buffer usage. Use it to validate cache assumptions and size work_mem/shared_buffers sensibly.
- File
  `postgres-lab/src/5_io_telemetry.py`
- Code
  ```python
  import os, psycopg
  with psycopg.connect(os.environ["DATABASE_URL"]) as conn, conn.cursor() as cur:
      cur.execute("select 1 from pg_class c join pg_namespace n on n.oid=c.relnamespace where n.nspname='pg_catalog' and c.relname='pg_stat_io'")
      if cur.fetchone():
          cur.execute("select backend_type, object, reads, writes from pg_stat_io limit 5")
          print(cur.fetchall())
      else:
          print({"pg_stat_io": False})
  ```

6) Time‑ordered IDs with UUID v7 (18+)
- How we solved it
  Prefer `uuidv7()` for locality and index friendliness. Gate by version and fall back to v4 if unavailable; consider sequence/ULID if strict order needed.
- File
  `postgres-lab/src/6_uuidv7_keys.py`
- Code
  ```python
  import os, psycopg
  with psycopg.connect(os.environ["DATABASE_URL"]) as conn, conn.cursor() as cur:
      try:
          cur.execute("select uuidv7() from generate_series(1,3)")
          print([r[0] for r in cur.fetchall()])
      except Exception as e:
          print({"uuidv7": False, "error": str(e)[:80]})
  ```

7) Fewer indexes via skip scans (18+)
- How we solved it
  On PG18, the planner can use B‑tree multi‑column indexes without leading column predicates (skip scans). Use EXPLAIN to confirm; avoid redundant secondary indexes.
- File
  `postgres-lab/src/7_skip_scans_explain.py`
- Code
  ```python
  import os, psycopg, random
  with psycopg.connect(os.environ["DATABASE_URL"]) as conn, conn.cursor() as cur:
      cur.execute("drop table if exists sd")
      cur.execute("create table sd(a int, b int)")
      cur.execute("create index on sd(a,b)")
      for i in range(1000):
          cur.execute("insert into sd(a,b) values (%s,%s)", (random.randint(0,50), random.randint(0,50)))
      cur.execute("explain select * from sd where b=10 order by a limit 5")
      print("\n".join(r[0] for r in cur.fetchall()))
  ```

8) Time-series: partitioning for fast prune
- How we solved it
  Use declarative range partitioning on a date/timestamp column so queries prune partitions and maintenance runs per-partition.
- File
  `postgres-lab/src/8_partitioned_timeseries.py`
- Code
  ```python
  import os, psycopg
  with psycopg.connect(os.environ["DATABASE_URL"]) as conn, conn.cursor() as cur:
      cur.execute("drop table if exists ev cascade")
      cur.execute("create table ev(dt date, user_id int, amount numeric) partition by range (dt)")
      cur.execute("create table ev_2024_10 partition of ev for values from ('2024-10-01') to ('2024-11-01')")
      cur.execute("create table ev_2024_11 partition of ev for values from ('2024-11-01') to ('2024-12-01')")
      cur.execute("explain select * from ev where dt >= '2024-11-10' and dt < '2024-11-15'")
      print("\n".join(r[0] for r in cur.fetchall()))
  ```

9) Full‑text search (FTS) with GIN
- How we solved it
  Use `tsvector` + GIN index with `to_tsvector()` and query with `plainto_tsquery()`.
- File
  `postgres-lab/src/9_full_text_search.py`
- Code
  ```python
  import os, psycopg
  with psycopg.connect(os.environ["DATABASE_URL"]) as conn, conn.cursor() as cur:
      cur.execute("drop table if exists docs")
      cur.execute("create table docs(id serial primary key, body text)")
      cur.execute("create index if not exists docs_fts on docs using gin(to_tsvector('english', body))")
      cur.executemany("insert into docs(body) values (%s)", [
          ("Postgres search is great",),
          ("Transactions and indexes",),
          ("Searching text with tsquery",),
      ])
      cur.execute("select id from docs where to_tsvector('english',body) @@ plainto_tsquery('english', %s)", ("search",))
      print(cur.fetchall())
  ```

10) Lock contention — diagnose and avoid
- How we solved it
  Reproduce a blocking row lock with two connections; use `lock_timeout` and appropriate indexing/ordering to reduce hot locks.
- File
  `postgres-lab/src/10_lock_contention.py`
- Code
  ```python
  import os, psycopg
  dsn=os.environ["DATABASE_URL"]
  a=psycopg.connect(dsn); b=psycopg.connect(dsn)
  with a, a.cursor() as ca:
      ca.execute("drop table if exists t"); ca.execute("create table t(id int primary key, v int)"); ca.execute("insert into t values (1,10)"); a.commit()
      ca.execute("begin"); ca.execute("update t set v=11 where id=1")  # hold row lock
      with b, b.cursor() as cb:
          cb.execute("set lock_timeout='300ms'")
          try:
              cb.execute("update t set v=12 where id=1")
          except Exception as e:
              print("blocked->timeout:", str(e)[:80])
  ```

11) Deadlock demo — detect and resolve
- How we solved it
  Show two transactions locking rows in opposite order; Postgres aborts one with a deadlock error.
- File
  `postgres-lab/src/11_deadlock_demo.py`
- Code
  ```python
  import os, psycopg
  dsn=os.environ["DATABASE_URL"]
  a=psycopg.connect(dsn); b=psycopg.connect(dsn)
  with a, a.cursor() as ca, b, b.cursor() as cb:
      ca.execute("drop table if exists d"); ca.execute("create table d(id int primary key, v int)"); ca.executemany("insert into d values (%s,%s)", [(1,1),(2,2)]); a.commit()
      ca.execute("begin"); cb.execute("begin")
      ca.execute("update d set v=3 where id=1")
      cb.execute("update d set v=4 where id=2")
      try:
          cb.execute("update d set v=5 where id=1")
          ca.execute("update d set v=6 where id=2")
      except Exception as e:
          print("deadlock detected:", str(e)[:100])
  ```

12) Partial index for hot subset
- How we solved it
  Build GIN/B-tree partial indexes targeting hot rows only; reduce index size and write amp.
- File
  `postgres-lab/src/12_partial_index_hotset.py`
- Code
  ```python
  import os, psycopg, random
  with psycopg.connect(os.environ["DATABASE_URL"]) as conn, conn.cursor() as cur:
      cur.execute("drop table if exists e")
      cur.execute("create table e(id serial, active boolean, ts timestamptz default now(), v int)")
      cur.executemany("insert into e(active,v) values (%s,%s)", [ (i%5==0, i) for i in range(1000) ])
      cur.execute("create index on e(ts) where active")
      cur.execute("explain select * from e where active and ts > now() - interval '1 day'")
      print("\n".join(r[0] for r in cur.fetchall()))
  ```

13) Materialized views + concurrent refresh
- How we solved it
  Use `REFRESH MATERIALIZED VIEW CONCURRENTLY` with a unique index to avoid blocking readers.
- File
  `postgres-lab/src/13_materialized_views_refresh.py`
- Code
  ```python
  import os, psycopg
  with psycopg.connect(os.environ["DATABASE_URL"]) as conn, conn.cursor() as cur:
      cur.execute("drop table if exists sales")
      cur.execute("create table sales(id serial primary key, amt int)")
      cur.execute("insert into sales(amt) select 1 from generate_series(1,1000)")
      cur.execute("drop materialized view if exists sales_mv")
      cur.execute("create materialized view sales_mv as select 1 as k, count(*) cnt from sales")
      cur.execute("create unique index if not exists sales_mv_k_idx on sales_mv(k)")
      cur.execute("refresh materialized view concurrently sales_mv")
      cur.execute("select * from sales_mv")
      print(cur.fetchall())
  ```

14) Parallel query knobs
- How we solved it
  Adjust session GUCs (`max_parallel_workers_per_gather`, costs) and inspect EXPLAIN for parallel plans.
- File
  `postgres-lab/src/14_parallel_query_knobs.py`
- Code
  ```python
  import os, psycopg
  with psycopg.connect(os.environ["DATABASE_URL"]) as conn, conn.cursor() as cur:
      cur.execute("set max_parallel_workers_per_gather=2")
      cur.execute("drop table if exists big"); cur.execute("create table big(x int)")
      cur.execute("insert into big select generate_series(1,200000)")
      cur.execute("explain select sum(x) from big")
      print("\n".join(r[0] for r in cur.fetchall()))
  ```

15) Advisory locks for distributed mutex
- How we solved it
  Use `pg_try_advisory_lock()` for coarse-grained cross-process mutexes keyed by business IDs.
- File
  `postgres-lab/src/15_advisory_locks.py`
- Code
  ```python
  import os, psycopg
  dsn=os.environ["DATABASE_URL"]; a=psycopg.connect(dsn); b=psycopg.connect(dsn)
  with a, a.cursor() as ca, b, b.cursor() as cb:
      ca.execute("select pg_try_advisory_lock(42)"); print("A lock:", ca.fetchone()[0])
      cb.execute("select pg_try_advisory_lock(42)"); print("B lock:", cb.fetchone()[0])
      ca.execute("select pg_advisory_unlock(42)")
  ```

16) Window functions for rollups
- How we solved it
  Use window functions for running totals, ranks, and percentiles without subqueries.
- File
  `postgres-lab/src/16_window_rollups.py`
- Code
  ```python
  import os, psycopg
  with psycopg.connect(os.environ["DATABASE_URL"]) as conn, conn.cursor() as cur:
      cur.execute("drop table if exists tx")
      cur.execute("create table tx(id serial, user_id int, amount int)")
      cur.executemany("insert into tx(user_id,amount) values (%s,%s)", [(1,10),(1,5),(2,7),(1,3),(2,6)])
      cur.execute("select id,user_id,amount, sum(amount) over (partition by user_id order by id) as running from tx")
      print(cur.fetchall())
  ```

17) Generated columns to index JSON
- How we solved it
  Use STORED generated columns to extract JSON keys and index them for fast lookup.
- File
  `postgres-lab/src/17_generated_columns_json.py`
- Code
  ```python
  import os, psycopg
  with psycopg.connect(os.environ["DATABASE_URL"]) as conn, conn.cursor() as cur:
      cur.execute("drop table if exists items")
      cur.execute("create table items(data jsonb, sku text generated always as ((data->>'sku')) stored)")
      cur.execute("create unique index if not exists items_sku_idx on items(sku)")
      cur.execute("insert into items(data) values (%s)", (psycopg.adapters.Json({"sku":"A1","name":"x"}),))
      cur.execute("select sku from items where sku='A1'")
      print(cur.fetchall())
  ```

Next: [Python Fanout Publisher](../python-fanout-publisher/problems.md)

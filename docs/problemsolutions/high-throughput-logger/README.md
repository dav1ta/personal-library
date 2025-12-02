# High-Throughput Logger

Enterprise-grade log ingestion pipeline using NATS JetStream, Vector, and ClickHouse.

## Architecture

```
Python App -> NATS JetStream -> Vector -> ClickHouse
                                    |
                                    v
                              Prometheus -> Grafana
```

## Quick Start

### 1. Start Infrastructure

```bash
docker-compose up -d
```

### 2. Install Python Dependencies

```bash
pip install -r requirements.txt
```

### 3. Run Log Simulator

```bash
python log_simulator.py
```

## Access Points

- **NATS Dashboard**: http://localhost:8222
- **Tabix (ClickHouse UI)**: http://localhost:8090
- **Grafana**: http://localhost:3000 (admin/admin)
- **Prometheus**: http://localhost:9090
- **Vector API**: http://localhost:8686

## ClickHouse Connection

- **Host**: localhost
- **Port**: 8123 (HTTP) or 9010 (Native)
- **User**: `default`
- **Password**: *(empty)*
- **Database**: `default`

## Create ClickHouse Table

Before running the simulator, create the table:

```sql
CREATE TABLE app_logs (
    timestamp DateTime64(3),
    service_id String,
    level String,
    message String,
    trace_id String,
    user_id String,
    ingested_at DateTime64(3)
) ENGINE = MergeTree()
ORDER BY (service_id, timestamp);
```

Run this in Tabix or via CLI:

```bash
docker exec -it clickhouse clickhouse-client
```

## Performance Tuning

Adjust `LOGS_PER_SECOND` in `log_simulator.py` to test different loads:
- **100/sec**: Low load testing
- **1,000/sec**: Medium load
- **10,000/sec**: High load (requires tuning)

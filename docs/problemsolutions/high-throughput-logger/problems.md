# Problem 1: Enterprise Log Ingestion Pipeline

**Real-World Scenario:**
In a large-scale distributed system, thousands of microservices generate logs. Directly coupling these services to the aggregation layer (Vector/Logstash) or Database is dangerous.

**Architecture:**
`Go Service` -> `NATS JetStream` -> `Vector` -> `ClickHouse`

## Architectural Decisions & Necessity

| Problem | Solution | Necessity (Why?) |
| :--- | :--- | :--- |
| **Slow Consumer (Backpressure)**<br>If DB is slow, the App blocks/crashes. | **Async Message Broker (NATS)**<br>App writes to NATS (RAM/Disk) instantly. NATS absorbs the spike. | **Critical**<br>Prevents cascading failures. Your API should never go down just because the Logger is slow. |
| **Data Loss on Restart**<br>Collector restarts = dropped logs. | **Durable Stream (JetStream)**<br>Persist logs to disk before processing. | **Critical**<br>Zero data loss is required for billing, audit, and debugging. |
| **"ETL Spaghetti"**<br>Parsing logic scattered across 50 apps. | **Centralized Pipeline (Vector)**<br>Parse, rename, and scrub PII in one place. | **High**<br>Drastically reduces maintenance. Schema changes happen in 1 config, not 50 codebases. |
| **Database Overload**<br>10k apps opening connections to DB. | **Connection Pooling (Vector)**<br>Vector merges streams into 1 connection. | **Critical**<br>ClickHouse/DBs will crash under thousands of concurrent connections. |
| **Write Performance**<br>Single-row inserts are too slow. | **Batching (Vector)**<br>Group 10k logs into 1 bulk write. | **Critical**<br>Required to reach 1M+ events/sec. Single inserts are ~1000x slower. |
| **Vendor Lock-in**<br>Hard to switch from ClickHouse to S3/Datadog. | **Routing Layer (Vector)**<br>Config-based routing to multiple sinks. | **Medium**<br>Future-proofing. Allows changing storage without rewriting application code. |

## Production Readiness Assessment (How good is this build?)

| Category | Grade | Notes |
| :--- | :--- | :--- |
| **Architecture** | **A+** | You are using the exact stack used by Uber/Cloudflare. It scales linearly. |
| **Resilience** | **A** | NATS guarantees delivery. Vector handles retries. |
| **Performance** | **A** | Batching + Binary Protocols (TCP/NATS) + ClickHouse is as fast as it gets. |
| **Observability** | **F** | **MISSING**. If Vector stops writing, you won't know until customers complain. |
| **Data Quality** | **C** | **MISSING**. If an app sends bad JSON, it might block the pipeline. |
| **Security** | **D** | **MISSING**. No TLS (Encryption) or Auth (User/Pass) configured yet. |

## Missing Solutions (The "Clear Picture")

To make this a true "Enterprise Platform", you are missing these 3 components:

### 1. Pipeline Observability (Monitoring the Monitor)
*   **Problem**: Who watches the watcher? If Vector's buffer fills up, or ClickHouse rejects inserts, you need to know.
*   **Solution**:
    *   **Prometheus**: Scrape Vector's internal metrics (`port 9598`).
    *   **Grafana**: Dashboard showing "Events In vs Events Out", "NATS Lag", and "ClickHouse Errors".
    *   **Alerts**: PageDuty if `NATS Lag > 10,000 messages`.

### 2. Dead Letter Queues (DLQ)
*   **Problem**: A service sends a log with a timestamp `2025-99-99`. ClickHouse rejects it. Vector retries... forever. The pipeline clogs.
*   **Solution**: Configure Vector to send "failed" events to a separate NATS Subject (`logs.dlq`).
    *   **Benefit**: The main pipeline keeps flowing. You can inspect the bad logs later.

### 3. Data Lifecycle Management (TTL)
*   **Problem**: ClickHouse fills up the disk in 3 months. Server crashes.
*   **Solution**:
    *   **ClickHouse TTL**: `ALTER TABLE logs MODIFY TTL timestamp + INTERVAL 30 DAY DELETE`.
    *   **Tiered Storage**: Move logs > 7 days old to S3 (Cheaper) before deleting.

Next: [Job Queue Service](../job-queue-service/problems.md)

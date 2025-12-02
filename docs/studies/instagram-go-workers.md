# 🔥 Instagram – Migrating Async Jobs to Celery → Custom Go Workers

## 1. Background

Instagram processes massive volumes of media:

- Users upload photos and videos continuously.
- Each upload triggers a pipeline: validation, transcoding, thumbnail generation, metadata extraction, and storage.
- Many of these steps are handled asynchronously via background jobs.

Originally, Instagram relied heavily on Python-based Celery workers for async job processing.

## 2. Problem

From all.md:

- Problem: Media processing jobs lagged behind; Celery couldn’t handle burst traffic.
- Cause: Python workers → high CPU load + slow scheduling.
- Solution:
  - Built Go-based job workers with priority queues.
  - Used S3 multipart uploads + parallelism.
  - Implemented idempotent jobs for retry correctness.
- Impact: Latency dropped massively; backlog disappeared.

Practically, this meant:

- During spikes (e.g., new features, viral content, regional events), job queues grew faster than they could be drained.
- Media processing lagged, causing delays between upload and availability.
- Retries and failures worsened load during peak times.

## 3. Root Causes

### 3.1 Python/Celery worker overhead

Celery-based workers:

- Used Python, with its interpreter and GIL overhead.
- Created many processes or threads to handle concurrency.
- Incurred significant per-task CPU and memory overhead.

Under heavy load:

- CPU utilization ballooned.
- Context switching and scheduling overhead increased.
- Throughput per machine was limited.

### 3.2 Scheduling and prioritization limits

Celery’s default scheduling:

- Did not always prioritize time-sensitive media jobs correctly.
- Made it difficult to isolate high-priority tasks (e.g., fresh uploads) from lower-priority background work.

Effect:

- Backlogs built up, and newer tasks could wait behind older, less important jobs.
- Spikes in one queue could affect others.

### 3.3 Inefficient handling of large media uploads

Handling large media files:

- Often involved copying data multiple times.
- Inefficient upload and storage strategies increased latency and resource usage.

Without careful design:

- Network and disk IO paths became bottlenecks.
- Retries on large uploads consumed disproportionate capacity.

## 4. Architecture Before (Simplified)

```text
User upload
   │
   ▼
┌──────────────────────────┐
│ Web/API Frontend (Python)│
└───────────┬──────────────┘
            │  Enqueue jobs
            ▼
┌──────────────────────────┐
│ Celery Worker Pool       │
│  - Media processing      │
│  - Thumbnails, transcoding
└───────────┬──────────────┘
            │
            ▼
        Storage (e.g., S3)
```

Characteristics:

- Celery workers executed Python code for all job types.
- Limited prioritization and isolation between critical and non-critical tasks.
- Scaling focused on adding more workers, but per-worker efficiency was constrained.

## 5. Architecture After (Optimized)

Instagram moved to a custom Go-based worker system with:

- Priority queues for jobs.
- Efficient, parallel S3 multipart uploads.
- Idempotent job design for safe retries.

Conceptual view:

```text
User upload
   │
   ▼
┌──────────────────────────┐
│ Web/API Frontend         │
└───────────┬──────────────┘
            │  Enqueue jobs
            ▼
┌─────────────────────────────────────┐
│ Go Worker System                    │
│  - Priority queues                  │
│  - Media processing & transcoding   │
│  - Idempotent job execution         │
└───────────┬─────────────────────────┘
            │
            ▼
       S3 / Media Storage
```

Python and Celery can still orchestrate some flows, but the hot path for heavy media processing is handled by Go workers.

## 6. How They Implemented the Fix (Step by Step)

### 6.1 Build Go-based job workers

Motivations for Go:

- Efficient concurrency model (goroutines, channels).
- Lower per-task overhead compared to Python processes.
- Better CPU and memory utilization.

Implementation aspects:

- Define clear job types for media tasks (e.g., transcode video, generate thumbnail).
- Implement workers in Go:

  - Long-running processes consuming jobs from queues.
  - Concurrency tuned via goroutines and worker pools.
  - Minimal allocations in tight loops to maximize throughput.

Result:

- Higher throughput per host.
- More predictable CPU usage under bursty load.

### 6.2 Introduce priority queues

Not all jobs are equal:

- Fresh uploads and user-visible processing are high priority.
- Some maintenance or reprocessing tasks are lower priority.

The new system uses priority queues:

- Separate queues or priority levels for different job categories.
- Schedulers and workers prioritize urgent tasks.

Behavior:

- During spikes, fresh upload processing remains responsive.
- Lower-priority jobs are delayed but not lost.

This prevents backlog of less important work from blocking critical media paths.

### 6.3 Use S3 multipart uploads with parallelism

Large media files are stored (e.g., in S3) using multipart uploads:

- Split large files into chunks.
- Upload chunks in parallel.
- Complete the multipart upload when all parts arrive.

Benefits:

- Better network utilization and throughput for large files.
- Retries can target specific failed parts, not the entire file.
- Parallelization reduces overall upload time.

Workers:

- Coordinate multipart uploads as part of job execution.
- Balance concurrency to avoid overwhelming network or storage.

### 6.4 Design idempotent jobs for safe retries

In large distributed systems, failures happen:

- Network timeouts.
- Worker crashes or restarts.
- Partial progress on jobs.

To handle this safely:

- Jobs are designed to be idempotent:

  - Re-running a job produces the same end state.
  - Operations check for existing outputs and states before acting.

Examples:

- Before generating a thumbnail, check if it already exists.
- Before completing a multipart upload, verify if it was already completed.

Effects:

- Retries do not double-process or corrupt media.
- The system can be aggressive with retries without fear of inconsistency.

### 6.5 Integrate with existing infrastructure

Migration steps:

- Keep Celery for orchestration and lightweight tasks where appropriate.
- Route heavy media jobs to the Go worker system via a queue or job service.
- Gradually shift traffic for specific job types from Celery to Go workers.
- Monitor backlog, latency, and error rates to validate improvements.

This incremental approach reduced risk while the new system proved itself.

## 7. Performance Results

From the original summary:

- Impact: Latency dropped massively; backlog disappeared.

Practically, this means:

- Queues drained quickly even during spikes.
- Media became available to users shortly after upload, maintaining a responsive experience.
- The system handled bursts of traffic without long delays or cascading failures.

## 8. Lessons and Reusable Patterns

### 8.1 Use the right runtime for heavy background work

- Dynamic languages are great for orchestration and business logic.
- CPU-intensive, massively parallel job processing often benefits from languages like Go or Rust.

### 8.2 Prioritize jobs explicitly

- Introduce priority queues for critical vs. non-critical work.
- Protect latency-sensitive operations from being starved by background tasks.

### 8.3 Exploit storage system capabilities

- Use multipart uploads and parallelism for large objects.
- Design jobs to take advantage of storage features (e.g., checksums, existing object checks).

### 8.4 Make jobs idempotent by design

- Enable safe retries in the face of partial failures.
- Avoid duplicate work and data corruption.

### 8.5 Migrate incrementally

- Keep existing systems for control and orchestration while offloading hot paths.
- Move specific, well-defined job types first to validate the new architecture.

These patterns apply broadly to large-scale background processing systems handling media, data pipelines, or any workload with bursts and heavy CPU/IO requirements.

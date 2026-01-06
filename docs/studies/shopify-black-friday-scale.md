# 🔥 Shopify – Handling Black Friday Traffic Spikes

## 1. Background

Shopify powers online stores for hundreds of thousands of merchants. On events like Black Friday and Cyber Monday:

- Traffic can spike 10–20× above normal.
- Spikes are highly bursty and synchronized across many stores.
- Checkout is the most critical path: if it fails or slows down, everyone loses revenue.

Originally, the checkout flow lived inside a centralized Rails monolith that handled many responsibilities (storefront rendering, business logic, admin, etc.).

## 2. Problem

From all.md:

- Problem: Checkout service overloaded when traffic spiked 20×.
- Cause: Centralized Rails monolith couldn’t horizontally scale.
- Solution:
  - Broke out the checkout pipeline into Go microservices.
  - Added Kafka-driven async workflows.
  - Used read replicas with aggressive caching.
  - Introduced rate-limiting per merchant.
- Impact: Stable at 99.999% uptime during BF.

In more detail:

- During peak events, the monolith hit CPU and database limits.
- Throughput did not scale linearly with added instances due to shared bottlenecks.
- Tail latency for checkout requests rose sharply under load.
- Failures in one area could affect many stores because of tight coupling.

## 3. Root Causes

### 3.1 Monolithic architecture with shared bottlenecks

The Rails monolith:

- Served many kinds of traffic (browsing, checkout, admin, APIs).
- Shared a primary database and cache.
- Coupled business logic, routing, and rendering in a single deployable unit.

Under extreme load:

- Critical checkout paths contended for resources with less critical traffic.
- Scaling required scaling everything together, which was expensive and still limited by the database.

### 3.2 Limited horizontal scalability of the hot path

Rails and the monolithic design:

- Relied heavily on synchronous web requests and a shared DB.
- Increased instance counts up to a point, but contention on the DB and cache limited gains.
- Tight coupling made it difficult to isolate and optimize checkout separately.

### 3.3 Synchronous workflows for non-critical steps

Some steps in the order lifecycle:

- Were handled inline in the checkout request (e.g., some downstream updates or notifications).
- Increased latency and made the system more fragile under spikes.

### 3.4 Uneven load across merchants

- Popular merchants and promotions produced disproportionate traffic.
- A single large merchant could strain shared resources, affecting others.

## 4. Architecture Before (Simplified)

```text
Client
  │
  ▼
┌─────────────────────────────┐
│ Rails Monolith              │
│  - Storefront rendering     │
│  - Cart & checkout logic    │
│  - Admin & APIs             │
└───────────┬─────────────────┘
            │
            ▼
        Primary Database
         + Shared Cache
```

Characteristics:

- Single codebase and deployment.
- Shared DB and cache for many concerns.
- Limited isolation between features and tenants.

## 5. Architecture After (Optimized)

Shopify extracted checkout into a dedicated, horizontally scalable pipeline built with Go microservices and backed by streaming and caching:

```text
Client
  │
  ▼
┌──────────────────────────────┐
│ Checkout Edge / API          │
└───────────┬──────────────────┘
            │
            ▼
┌──────────────────────────────┐
│ Go Checkout Microservices    │
│  - Cart & pricing            │
│  - Payment & fraud checks    │
│  - Order creation            │
└───────────┬──────────────────┘
            │
            ▼
       Kafka / Async Workflows
            │
            ▼
   Downstream Services & DBs
```

Key changes:

- Checkout pipeline extracted into Go services optimized for performance and concurrency.
- Non-critical work moved to Kafka-driven async workflows.
- Data access optimized via read replicas and aggressive caching.
- Per-merchant rate limiting protects the platform from individual spikes.

## 6. How They Implemented the Fix (Step by Step)

### 6.1 Extract the checkout pipeline into Go microservices

Motivations for Go:

- Efficient concurrency and performance compared to Ruby in hot paths.
- Strong tooling and runtime for networked services.
- Easier to control memory and CPU usage per service.

Steps:

1. Identify the core checkout workflow:
   - Cart validation.
   - Pricing/taxes.
   - Payment processing.
   - Order creation.
2. Define clear service boundaries and APIs for these steps.
3. Implement services in Go with:

   - Lean HTTP/gRPC handlers.
   - Efficient JSON/binary encoding where appropriate.
   - Minimal per-request allocation.

4. Migrate traffic gradually from the monolith to the new Go-based pipeline.

Benefits:

- Independent scaling of checkout services.
- Tighter control over resource usage.
- Better latency behavior at high concurrency.

### 6.2 Introduce Kafka-driven async workflows

Not all checkout-related work must happen synchronously:

- Notifications, analytics, some inventory and fulfillment updates can be asynchronous.
- This reduces the amount of work in the critical path for checkout.

Implementation:

- Use Kafka topics to represent events: `order_created`, `payment_authorized`, etc.
- Checkout services publish events when key milestones occur.
- Downstream services subscribe and perform additional processing.

Effects:

- Checkout requests focus on essential work: validating, charging, and confirming orders.
- System becomes more resilient to spikes, as asynchronous consumers can scale independently.
- Failures in downstream work do not immediately impact the core checkout path.

### 6.3 Use read replicas and aggressive caching

To relieve pressure on the primary database:

- Add read replicas for frequently accessed data (product info, shop settings, etc.).
- Implement aggressive caching:

  - In-memory caches at the service layer.
  - Distributed caches for hot, shared data.

Patterns:

- Cache-aside: services consult caches first, then fall back to DB, and populate caches on misses.
- Short TTLs or invalidation strategies to keep data fresh enough for checkout correctness.

Benefits:

- Reduced load on the primary DB.
- Lower latency for common reads.
- More predictable performance under bursts.

### 6.4 Introduce per-merchant rate limiting

To prevent a few merchants from overwhelming the system:

- Implement rate limiting keyed by merchant/store ID.
- Enforce limits on:

  - Request rate.
  - Concurrent checkout sessions.
  - Potentially on specific heavy operations.

Behavior:

- When a merchant exceeds defined limits, the platform:
  - Slows requests (soft limiting) or returns throttling responses.
  - Protects global system health, preserving capacity for others.

This ensures:

- Fairness across merchants during global events.
- Better overall uptime and quality of service.

### 6.5 Harden and tune the platform for peak events

Additional practices:

- Capacity planning and load testing specifically for 20×–plus traffic.
- Feature flags and “circuit breakers” to disable non-critical features during incidents.
- Observability (metrics, traces, logs) focused on the checkout path.

Combined, these steps allowed Shopify to run Black Friday without major incidents.

## 7. Performance Results

From the original summary:

- Impact: Stable at 99.999% uptime during BF.

Implications:

- Checkout remained available and performant despite massive traffic spikes.
- The platform could absorb large, sudden increases in load without widespread failures.
- Extraction into microservices, plus async workflows and caching, significantly improved scalability.

## 8. Lessons and Reusable Patterns

### 8.1 Extract critical paths from monoliths

- Identify the most business-critical and latency-sensitive flows.
- Carve them out into services that can be scaled and optimized independently.

### 8.2 Use async workflows for non-critical work

- Move logging, notifications, some inventory updates, and analytics off the synchronous path.
- Use reliable messaging (Kafka, etc.) to decouple workflows.

### 8.3 Lean on read replicas and caches under read-heavy load

- Use read replicas for frequently accessed, less mutable data.
- Combine with caches to further reduce DB load and latency.

### 8.4 Apply per-tenant rate limiting

- Protect shared infrastructure from “noisy neighbors”.
- Ensure fair resource allocation across tenants.

### 8.5 Plan and test explicitly for peak events

- Treat events like Black Friday as first-class engineering requirements.
- Load-test and simulate surge scenarios.
- Implement controls and flags to shed non-essential load when needed.

These patterns are valuable for any multi-tenant SaaS platform facing periodic extreme spikes in traffic.

Next: [Twitch Chat Scale](twitch-chat-scale.md)

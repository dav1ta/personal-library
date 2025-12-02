# Problem 1: Per-User Rate Limits Without Starving the System

**Real-World Scenario:**  
A backend API gets hammered by a few aggressive clients. Without rate limits, noisy neighbors consume most capacity and well-behaved users see timeouts and high latency.

**System Design Pattern:**  
Token Bucket Rate Limiting, Sliding Window Counters

**Solution Logic:**  
Implement a gateway that enforces per-user (or per-API key) rate limits using a token bucket. Each request consumes a token; tokens refill at a fixed rate. When a bucket is empty, the gateway rejects or delays requests for that user instead of letting them overwhelm the backend. This pushes fairness and backpressure to the edge of the system while keeping the implementation simple and observable.


# Problem 2: Circuit Breaker Around a Flaky Backend

**Real-World Scenario:**  
The gateway forwards requests to a backend that sometimes becomes slow or fails. Without protection, the gateway keeps sending traffic, building up queues and tying up resources, causing cascading failures.

**System Design Pattern:**  
Circuit Breaker, Timeout + Retry with Jitter

**Solution Logic:**  
Wrap backend calls in a circuit breaker with three states: CLOSED (normal), OPEN (fail fast), HALF-OPEN (probe). Track failures and timeouts in a rolling window; when they cross a threshold, OPEN the breaker to stop sending traffic and immediately return a degraded response. After a cool-down, allow a few probe requests in HALF-OPEN to see if the backend recovered. Use timeouts and limited retries to avoid stuck calls.


# Problem 3: Request Collapsing Under Load

**Real-World Scenario:**  
Many clients request the same resource (e.g., `/config` or `/profile/123`) at once. Naively, the gateway forwards each request, multiplying load on the backend, even though the responses would be identical.

**System Design Pattern:**  
Request Coalescing (Collapsing), Read-Through Caching

**Solution Logic:**  
Detect when multiple in-flight requests target the same cacheable key. Only send one upstream request and have the others wait on its result. Once the backend responds, fan out the result to all waiting callers and populate a short-lived cache entry. This reduces duplicated work and protects the backend during traffic spikes while keeping latency acceptable for callers.


## Project Plan (No Code Yet)

- Define a minimal HTTP surface: a tiny backend API and how the gateway forwards to it.
- Design a per-user token bucket abstraction (in-memory first) and decide what metrics you will expose.
- Sketch the circuit breaker state machine and choose concrete thresholds (failure counts, time windows, cool-down).
- Decide which routes are cacheable and how to key them for request collapsing and caching.
- Plan observability: a `/metrics` or `/status` endpoint showing breaker state, rate-limit stats, and cache hit rate.


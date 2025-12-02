# Problem 1: Origin Overload Without Caching

**Real-World Scenario:**  
All clients fetch static assets (images, JS bundles, video segments) directly from a single origin server. As traffic grows, the origin saturates its NIC and disk, causing slow responses and timeouts.

**System Design Pattern:**  
Cache-Aside, Read Replicas (Edge Nodes)

**Solution Logic:**  
Introduce edge nodes that maintain an HTTP cache of content. Clients talk to the nearest edge; on cache miss, the edge fetches from origin, stores the response, and serves it. Subsequent requests hit the edge cache, reducing load on origin. The core behavior is cache-aside: the application code (edges) explicitly loads into and reads from the cache.


# Problem 2: Routing Requests to Multiple Edges

**Real-World Scenario:**  
As load grows, you deploy multiple edge nodes. Without a routing strategy, some edges are overloaded while others are idle, and clients may bounce between edges, missing caches and increasing origin traffic.

**System Design Pattern:**  
Consistent Hashing, Client-Aware Routing

**Solution Logic:**  
Use consistent hashing on a stable key (e.g., user ID, client IP, or asset key) to map each request to an edge node. This stabilizes which edge serves which content, improving cache locality. When adding or removing edges, consistent hashing minimizes key movement, preserving most caches and smoothing load.


# Problem 3: Handling Origin Failure Gracefully

**Real-World Scenario:**  
The origin server becomes slow or unavailable. Edges keep retrying misses, amplifying the failure and causing high latency and error storms for all clients.

**System Design Pattern:**  
Circuit Breaker at Edge, Stale-While-Revalidate Caching

**Solution Logic:**  
Each edge tracks origin health. If origin fetches repeatedly time out or fail, an edge opens a circuit breaker for origin calls and stops hitting it. Instead, it serves stale-but-recent cached content when available and fails fast otherwise. When the breaker half-opens, edges cautiously probe origin before resuming normal cache-fill behavior. This reduces blast radius and keeps the system partially useful during outages.


## Project Plan (No Code Yet)

- Define a minimal asset model (key + payload) and a simple origin HTTP API that serves them.
- Design an edge node: in-memory cache structure (key → value + TTL + stale metadata) and how it talks to origin.
- Choose a consistent hashing scheme for mapping keys to edge instances, even if simulated in one process at first.
- Define failure behavior when origin is slow or down: breaker thresholds, duration to serve stale content, when to give up.
- Plan metrics: cache hit ratio, origin QPS, per-edge load, and breaker state visibility.


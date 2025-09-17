# System Design Detailed Summary

## Table of Contents  
- [Preface](#preface)  
- [License](#license)  
- [Getting Started](#getting-started)  
- [Chapter I: IP & Networking](#chapter-i-ip-networking)  
- ...

---

## Preface  
Welcome, I'm glad you’re here!  
System design interviews can be open-ended and intimidating. The objective of this book is to help you learn fundamentals and advanced topics of system design, providing a strategy for interview preparation.

---

## License  
All rights reserved.  
This book, or any portion thereof, may not be reproduced or used without express written permission from the author.

---

## Getting Started

### What is System Design?  
System design defines the architecture, interfaces, and data for a system that meets specific requirements. It's about coherent and efficient systems meeting business needs, considering infrastructure, data storage, etc.

### Why is System Design Important?  
- **Early Decision Impact:** Decisions made early are hard to correct later.
- **Manage Architectural Changes:** Simplifies reasoning about system evolution.

---

## Chapter I: IP & Networking

### IP Addresses  
An IP address uniquely identifies a device on the internet or local network.

#### Versions  
- **IPv4:**  
  - 32-bit numeric notation.  
  - Example: `102.22.192.181`  
- **IPv6:**  
  - 128-bit hexadecimal notation.  
  - Example: `2001:0db8:85a3:0000:0000:8a2e:0370:7334`

#### Types  
- **Public:** One IP for whole network.  
- **Private:** Unique IP for each device on network.  
- **Static:** Unchanging IP, manually set.  
- **Dynamic:** Changes over time via DHCP.

### OSI Model  
Seven-layer model for network communication:
1. **Application:** User interface, protocols like HTTP, SMTP.
2. **Presentation:** Data translation, encryption, compression.
3. **Session:** Manages sessions between devices.
4. **Transport:** End-to-end communication, TCP/UDP.
5. **Network:** Routing, IP packet creation.
6. **Data Link:** Framing, MAC addresses.
7. **Physical:** Cables, switches, electrical signals.

### TCP and UDP

#### TCP (Transmission Control Protocol)
- Connection-oriented.
- Guarantees delivery, order, error-checking.
- Suitable for web pages, files, data reliability.

#### UDP (User Datagram Protocol)
- Connectionless.
- Minimal overhead, faster, no guarantee of delivery.
- Suited for real-time transmissions: video, VoIP.

| Feature      | TCP                       | UDP                       |
|--------------|---------------------------|---------------------------|
| Connection   | Connection-oriented       | Connectionless            |
| Delivery     | Guaranteed                | Best-effort               |
| Speed        | Slower                    | Faster                    |
| Use Cases    | HTTP, FTP, SMTP           | Streaming, DNS, VoIP      |

### Domain Name System (DNS)

#### Function
Translates human-readable domain names to IP addresses.

#### How DNS Works
1. Client queries resolver for `example.com`.
2. Resolver queries root nameserver.
3. Root directs to TLD nameserver.
4. TLD nameserver directs to authoritative server.
5. Authoritative server returns IP.
6. Resolver returns IP to client.

#### Server Types
- **DNS Resolver:** Intermediary between client and nameservers.
- **Root Server:** Directs to TLD servers.
- **TLD Server:** Manages domains like `.com`, `.org`.
- **Authoritative DNS Server:** Provides final IP answers for domains.

#### Query Types
- **Recursive:** Server fully resolves query.
- **Iterative:** Server gives best answer or referral.
- **Non-recursive:** Server returns cached or known answer immediately.

#### Record Types  
Common DNS records:
- **A:** IPv4 address.
- **AAAA:** IPv6 address.
- **CNAME:** Canonical name, alias.
- **MX:** Mail server.
- **TXT:** Text notes, often for security.
- **NS:** Name server.
- **SOA:** Start of Authority.
- **PTR:** Reverse lookup pointer.
- **CERT:** Certificate data.

---

## Chapter I Continued: Load Balancing, Clustering, Caching, etc.

### Load Balancing  
Distributes network traffic across multiple servers.

#### Purpose  
- Ensures high availability, reliability.
- Prevents any one server from overloading.

#### Algorithms  
- Round-robin  
- Weighted round-robin  
- Least connections  
- Least response time  
- Hashing, etc.

#### Types  
- **Software:** Flexible, cost-effective.
- **Hardware:** Dedicated devices, high speed.
- **DNS-based:** Uses DNS responses to balance load, but less reliable.

#### Features  
- Autoscaling, Sticky sessions, Health checks, Persistence, Encryption, Compression, Logging.

### Clustering  
Group of servers working together as one system.

#### Types  
- **Active-Active:** All nodes actively handle traffic.
- **Active-Passive:** Standby nodes take over if active fails.

#### Advantages  
- High availability  
- Scalability  
- Performance improvements  
- Cost efficiency

### Caching  
Stores data temporarily to improve retrieval speed.

#### Cache Types  
- **Write-through:** Write to cache and storage simultaneously.
- **Write-around:** Write directly to storage; cache on read.
- **Write-back:** Write to cache first, sync to storage later.

#### Eviction Policies  
- FIFO, LIFO, LRU, MRU, LFU, Random Replacement.

---

## Further Sections  
The rest of the content covers CDNs, Proxy Servers, Availability, Scalability, Storage, Databases (SQL & NoSQL), and more in detail. Each section follows similar structuring:

- **CDN:** Explains push/pull methods, advantages/disadvantages.
- **Proxy:** Types (forward/reverse) and use cases.
- **Availability:** Defines uptime percentages, formulas.
- **Scalability:** Vertical vs horizontal scaling pros and cons.
- **Storage:** RAID levels, differences between file/block/object storage, NAS, HDFS.
- **Databases:** 
  - **SQL:** Structured, ACID, relational.
  - **NoSQL:** Various types (document, key-value, graph, etc.), BASE model, scalability.

For detailed raw Markdown of additional sections, each follows the pattern above: heading, explanation, tables where applicable, and lists.

---

# Detailed System Design Markdown

## 64. Database Replication  
Replication shares information between databases to ensure consistency, reliability, fault tolerance, and accessibility.

### Master-Slave Replication  
- **Description:**  
  - Master handles reads/writes; slaves replicate writes and handle only reads.
  - Slaves can replicate further in a tree structure.
  - If the master fails, system may operate in read-only mode until a slave is promoted.
  
- **Advantages:**  
  - Backups possible with little impact on master.
  - Read scaling by directing reads to slaves.
  - Slaves can be updated or synced offline without downtime.
  
- **Disadvantages:**  
  - Additional hardware and complexity.
  - Potential downtime/data loss if master fails.
  - All writes must go to master.
  - Increased replication lag with many slaves.

### Master-Master Replication  
- **Description:**  
  - Both masters handle reads/writes and coordinate.
  - Continues operating with full capabilities if one master fails.
  
- **Advantages:**  
  - Load distribution across masters.
  - Quick, automatic failover.
  
- **Disadvantages:**  
  - More complex to configure.
  - Potential consistency issues or increased write latency.
  - Requires conflict resolution.

### Synchronous vs Asynchronous Replication  
- **Synchronous:**  
  - Data written to primary and replica simultaneously.
  - Ensures immediate consistency.
  
- **Asynchronous:**  
  - Data written to replica after primary write completes.
  - Near-real-time replication; more cost-effective.
  
## 67. Indexes  
Indexes speed up data retrieval at the cost of additional storage and slower writes.

- **Dense Index:**  
  - Index record for every table row.
  - Pros: Fast binary search, no ordering requirement.
  - Cons: High maintenance and memory usage.
  
- **Sparse Index:**  
  - Records for only some rows.
  - Pros: Less maintenance and memory.
  - Cons: Slower lookups due to scanning post binary search.
  
## 70. Normalization and Denormalization

### Keys and Dependencies  
- **Primary key, Composite key, Super key, Candidate key, Foreign key, Alternate key, Surrogate key.**  
- **Partial, Functional, Transitive Dependencies.**

### Anomalies  
- **Insertion anomaly:** Can't insert data without related attributes.  
- **Update anomaly:** Inconsistent updates due to redundancy.  
- **Deletion anomaly:** Removing data unintentionally removes additional data.

### Normalization  
Organizing data to reduce redundancy and avoid anomalies.

#### Normal Forms  
- **1NF:** No repeating groups, unique primary keys, separate related data.
- **2NF:** Meets 1NF, no partial dependencies.
- **3NF:** Meets 2NF, no transitive dependencies.
- **BCNF:** Strengthened 3NF; for every dependency X → Y, X is a superkey.

**Advantages:**  
- Reduces redundancy.
- Increases consistency, integrity.

**Disadvantages:**  
- Complex design.
- Potential slower performance due to joins.
- Maintenance overhead.

### Denormalization  
Adding redundant data to optimize read performance.

**Advantages:**  
- Faster retrieval.
- Simplified queries, fewer tables.

**Disadvantages:**  
- Expensive inserts/updates.
- Increased redundancy and inconsistency risk.

## 76. ACID and BASE Consistency Models

### ACID  
- **Atomicity:** All or nothing.  
- **Consistency:** Database remains valid.  
- **Isolation:** Transactions don't interfere.  
- **Durability:** Committed changes persist.

### BASE  
- **Basic Availability:** System mostly operational.  
- **Soft-state:** Not immediately consistent.  
- **Eventual consistency:** Consistency achieved over time.

**Trade-offs:**  
- ACID: Strong consistency, simpler reasoning.
- BASE: Scalability, performance at expense of immediate consistency.

## 79. CAP Theorem  
- **Consistency:** All nodes see same data simultaneously.
- **Availability:** Every request receives a response.
- **Partition tolerance:** System functions despite network partitions.

**Trade-off:** Must choose between consistency and availability during partitions.

### CAP Database Types  
- **CA:** Consistent and available, not partition-tolerant.
- **CP:** Consistent and partition-tolerant, may sacrifice availability.
- **AP:** Available and partition-tolerant, may sacrifice consistency.

## 82. PACELC Theorem  
Extends CAP by considering latency when there's no partitioning:  
- **PACELC:** On Partition (P): choose between Availability (A) and Consistency (C).  
  Else (E): choose between Latency (L) and Consistency (C).

## 83. Transactions  
A transaction is a unit of work executed fully or not at all.

### Transaction States  
- **Active:** Being executed.
- **Partially committed:** Final operation done.
- **Committed:** All operations successful.
- **Failed:** A check failed.
- **Aborted:** Rolled back due to failure.
- **Terminated:** Completed or safely ended.

## 85. Distributed Transactions  
Operations across multiple databases requiring coordination:
- **2PC (Two-Phase Commit):**  
  - **Prepare:** Coordinator collects readiness from participants.  
  - **Commit:** If all ready, commit; else rollback.
  - **Problems:** Node/coordinator crashes, blocking.

- **3PC (Three-Phase Commit):**  
  - Adds pre-commit phase to reduce blocking.
  - **Phases:** Prepare → Pre-commit → Commit.

### Sagas  
A sequence of local transactions with compensations for failures.

**Coordination Approaches:**  
- **Choreography:** Events trigger subsequent actions.
- **Orchestration:** Central coordinator directs actions.

**Problems:**  
- Hard to debug, risk of cyclic dependencies, testing complexity.

## 90. Sharding  
Dividing a database into smaller, more manageable pieces.

### Data Partitioning Methods  
- **Horizontal (Sharding):** Split table rows across shards.
- **Vertical:** Split columns across tables.

### Sharding Techniques  
- **Hash-based:** Uses hash functions; difficult dynamic changes.
- **List-based:** Partitions defined by list of values.
- **Range-based:** Partitions by continuous value ranges.
- **Composite:** Combines methods.

**Advantages:**  
- Improved availability, scalability, security, performance.

**Disadvantages:**  
- Increased complexity.
- Difficulty in performing joins across shards.
- Rebalancing challenges.

**When to use:**  
- Leverage commodity hardware.
- Geographical data distribution.
- Need for rapid scaling, better performance.

## 94. Consistent Hashing

**Problem:** Traditional hashing redistributes most keys when nodes change.

**Solution:**  
- Use a hash ring to assign both nodes and keys positions.
- Minimal key redistribution when nodes change.

### Virtual Nodes  
- Assign multiple positions (virtual nodes) to a single physical node.
- Improves load distribution and reduces hotspots.

### Data Replication  
- Replicate data across multiple nodes for durability and availability.

**Advantages:**  
- Predictable rapid scaling.
- Reduces hotspots.
- Facilitates partitioning.

**Disadvantages:**  
- Increased complexity.
- Possible uneven load distribution.
- Key management overhead.

**Examples:**  
- Apache Cassandra, Amazon DynamoDB use consistent hashing for partitioning.

## 101. Database Federation  
Combines multiple distinct databases into one logical database.

**Characteristics:**  
- Transparency, heterogeneity, extensibility, autonomy, integration.

**Advantages:**  
- Flexible sharing, autonomy, unified access.

**Disadvantages:**  
- Complexity in joins, query performance issues.

## 103. N-tier Architecture  
Divides application into logical layers (tiers) to separate concerns.

### Types  
- **3-Tier:** Presentation, Business Logic, Data Access.
- **2-Tier:** Presentation directly communicates with Data Store.
- **Single-Tier:** All components on one machine.

**Advantages:**  
- Improved availability, security, scalability, maintainability.

**Disadvantages:**  
- Increased complexity, latency, cost, security management.

## 106. Message Brokers  
Software enabling communication between applications via message translation.

### Messaging Patterns  
- **Point-to-Point:** One sender to one receiver.
- **Publish-Subscribe:** One publisher to multiple subscribers.

**Examples:** NATS, Apache Kafka, RabbitMQ, ActiveMQ.

## 109. Message Queues  
Asynchronous service-to-service communication.

**Workflow:**  
1. Producer publishes a job.
2. Consumer retrieves and processes it.
3. Message deleted after processing.

**Advantages:**  
- Scalability, decoupling, performance, reliability.

**Key Features:**  
- Push/Pull delivery, FIFO, delayed delivery, at-least-once/exactly-once delivery, dead-letter queues, ordering, poison-pill messages, security, backpressure.

**Examples:** Amazon SQS, RabbitMQ, ActiveMQ, ZeroMQ.

## 113. Publish-Subscribe  
Asynchronous communication where publishers send messages to topics, subscribers receive them immediately.

**Advantages:**  
- Eliminates polling, dynamic targeting, decoupled scaling, simplified communication.

**Features:**  
- Push delivery, multiple protocols, fanout, filtering, durability, security.

**Examples:** Amazon SNS, Google Pub/Sub.

## 117. Enterprise Service Bus (ESB)  
Centralized integration layer for applications.

**Advantages:**  
- Developer productivity, scalability, resilience.

**Disadvantages:**  
- Single point of failure, complexity, difficult updates, configuration overhead.

**Examples:** Azure Service Bus, IBM App Connect, Apache Camel, Fuse ESB.

## 119. Monoliths and Microservices

### Monoliths  
Single self-contained application.

**Advantages:**  
- Simpler development, reliable communication, easy testing, ACID support.

**Disadvantages:**  
- Harder maintenance, tightly coupled, redeployment for changes, scalability issues.

### Microservices  
Collection of small, autonomous services.

**Characteristics:**  
- Loosely coupled, focused scope, business-aligned, resilient, maintainable.

**Advantages:**  
- Independent deployment/scaling, agility, fault tolerance, data isolation.

**Disadvantages:**  
- Distributed complexity, testing challenges, higher maintenance cost, inter-service communication issues, consistency.

**Best Practices:**  
- Domain-driven design, loose coupling, fault isolation, well-designed APIs, private data storage, decentralized teams, circuit breakers, backward-compatible API changes.

**Pitfalls:**  
- Poor service boundaries, underestimating distributed systems, shared databases, unclear ownership, non-idempotent operations, expecting full ACID in microservices, lack of fault tolerance design.

**Distributed Monolith Warning:**  
A system that appears microservices but is tightly coupled like a monolith. Signs include high inter-service dependencies, shared databases, low scalability. 

**Microservices vs SOA:**  
- SOA emphasizes reusability and shared services.
- Microservices emphasize small, autonomous, business-focused services.

**Why You Don't Need Microservices:**  
- Not necessary if complexity doesn't demand decoupled services.
- Evaluate organizational readiness, team size, business value before adopting.

## 127. Event-Driven Architecture (EDA)  
Uses events for communication, achieving loose coupling.

**Components:**  
- Event producers, routers, consumers.

**Patterns:**  
- Sagas, Publish-Subscribe, Event Sourcing, CQRS.

**Advantages:**  
- Decoupling, scalability, agility.

**Challenges:**  
- Guaranteed delivery, error handling, complexity, ordering.

**Use Cases:**  
- Real-time reporting, log processing, system integration, parallel processing.





## 130. Event Sourcing

- **Principle:**  
  Instead of storing current state only, store an append-only log of all state-changing events.
  
### Benefits
- Real-time data reporting.
- Fail-safety: reconstitute data from event store.
- Flexibility in storing any message type.
- Ideal for audit logs in high compliance systems.

### Drawbacks
- Requires highly efficient network infrastructure.
- Needs robust schema management (e.g., schema registry).
- Handling varied payloads across different events.

## 132. Command Query Responsibility Segregation (CQRS)

- **Pattern:**  
  Segregates system's read (query) and write (command) operations into different models.
  
### CQRS with Event Sourcing
- Use separate read/write data models.
- The event store serves as the write model.
- Materialized views derived from events serve as the read model.

### Advantages of CQRS
- Independent scaling of reads and writes.
- Simplified optimizations and architectural changes.
- Aligns closely with business logic.
- Avoids complex joins in queries.
- Clear boundaries in system behavior.

### Disadvantages of CQRS
- Increased design complexity.
- Potential for message failures/duplicates.
- Challenges dealing with eventual consistency.
- Higher maintenance effort.

### Use Cases for CQRS
- When read/write performance needs separate tuning.
- Systems evolving with multiple model versions.
- Integrating with other systems using event sourcing.
- Enhanced security ensuring correct domain entity writes.

## 134. API Gateway

- **Role:**  
  Sits between clients and backend services; single entry point offering tailored APIs, authentication, monitoring, load balancing, caching, rate limiting, logging, etc.

### Why Use an API Gateway?
- Microservices often offer fine-grained APIs; an API Gateway aggregates and simplifies client interactions.
- Provides centralized management and additional features.

### Desired Features
- Authentication and Authorization.
- Service discovery integration.
- Acts as a Reverse Proxy.
- Caching mechanisms.
- Security enforcement.
- Retry logic and Circuit breaking.
- Load balancing across services.
- Logging and Tracing.
- API composition.
- Rate limiting and throttling.
- Versioning and Routing.
- IP whitelisting/blacklisting.

### Pros and Cons

**Advantages:**
- Hides internal architecture.
- Centralized API view.
- Simplifies client logic.
- Enhances monitoring, analytics, and tracing.

**Disadvantages:**
- Potential single point of failure.
- Possible performance impact.
- Can become a bottleneck if under-scaled.
- Complex configuration.

## Backend For Frontend (BFF) Pattern

- **Concept:**  
  Create separate backend services optimized for specific frontend applications, reducing client-side logic needed for data transformation.

### When to Use BFF
- Avoid maintaining a generic backend for diverse frontends.
- Optimize backend for specific client requirements.
- Reduce frontend complexity in reformatting data.

**Example Technology:**  
GraphQL often acts as an effective BFF.

### Benefits
- Tailored responses for each frontend.
- Simplified client implementation.
- Centralizes transformation logic.

## 139. API Technologies: REST, GraphQL, gRPC

### REST
- **Definition:**  
  Architectural style using HTTP verbs to interact with resources via URLs.
- **Pros:**  
  Simple, flexible, good caching, decoupled client-server.
- **Cons:**  
  Over-fetching data, possibly multiple round trips.

### GraphQL
- **Definition:**  
  Query language for APIs that returns exactly requested data.
- **Pros:**  
  Eliminates over-fetching, strongly defined schema, efficient payload.
- **Cons:**  
  Server-side complexity, caching difficulty, versioning ambiguity, potential N+1 problem.

### gRPC
- **Definition:**  
  High-performance RPC framework using protocol buffers.
- **Pros:**  
  Lightweight, high performance, bi-directional streaming, code generation.
- **Cons:**  
  Limited browser support, steeper learning curve, less human-readable.

### Comparison Factors
- Coupling, chatty-ness, performance, integration complexity, caching, tooling, discoverability, versioning.

**Conclusion:**  
No single API technology is inherently "better"; choice depends on specific requirements and use cases.

## 149. Real-Time Communication Techniques

### Long Polling
- **Mechanism:**  
  Client sends request, server holds response until data available or timeout, then client reconnects.
- **Pros:**  
  Simple, widely supported.
- **Cons:**  
  Scalability issues, resource-intensive, potential ordering issues.

### WebSockets
- **Mechanism:**  
  Persistent, full-duplex communication channel over TCP, established via handshake.
- **Pros:**  
  Low overhead, bi-directional async messaging.
- **Cons:**  
  No automatic recovery of broken connections, older browser support declining.

### Server-Sent Events (SSE)
- **Mechanism:**  
  Unidirectional channel where server pushes updates to client over HTTP.
- **Pros:**  
  Simple to implement, good browser support, firewall-friendly.
- **Cons:**  
  Unidirectional, limited concurrent connections, no binary data support.

## 154. Geohashing and Quadtrees

### Geohashing
- Encodes latitude and longitude into short alphanumeric strings.
- Hierarchical spatial index: longer prefixes imply closer proximity.
- Cell size varies with geohash length.

**Use Cases:**  
- Simplified location storage and retrieval.
- Nearest neighbor searches.
- Data anonymity over geographic areas.

### Quadtrees
- Tree data structure partitioning 2D space into quadrants.
- Types include point, region, polygonal map quadtrees, etc.

**Uses:**  
- Spatial indexing and range queries.
- Computer graphics, image compression.
- Location-based services (e.g., maps, ride-sharing).

## 158. Circuit Breaker Pattern

- **Purpose:**  
  Prevents repeated attempts to perform an operation likely to fail, mitigating cascading failures.
  
### States
- **Closed:** Normal operation; monitor failures.
- **Open:** Fail fast; block calls after threshold exceeded.
- **Half-open:** Test limited requests; reset or revert based on success.

## 160. Rate Limiting

- **Goal:**  
  Control operation frequency to protect resources, mitigate DoS attacks, control costs.

### Algorithms
- **Leaky Bucket:** Queue with fixed processing rate; drops excess.
- **Token Bucket:** Requires token for each request; refills over time.
- **Fixed Window:** Count resets every fixed interval.
- **Sliding Log:** Track timestamps of each request.
- **Sliding Window:** Combines fixed window with weighted previous window values.

### Distributed Considerations
- **Inconsistencies:** Ensure global limits across nodes, avoid sticky sessions drawbacks.
- **Race Conditions:** Use atomic operations, distributed locks, or "set-then-get" strategies to safely update counters.

## 164. Service Discovery

- **Definition:**  
  Detecting and locating services dynamically in a distributed system.

### Patterns
- **Client-side discovery:** Clients query registry for service locations.
- **Server-side discovery:** Load balancer routes client requests based on registry info.

### Components
- **Service Registry:** Database of service instance locations; must be highly available.
- **Service Registration:** Services self-register or use third-party registration mechanisms.

### Tools
- etcd, Consul, Apache Zookeeper, etc.
- Service Mesh solutions like Istio and Envoy for managing service communication.

## 167. SLA, SLO, SLI

- **SLA (Service Level Agreement):**  
  Contract defining service promises.
- **SLO (Service Level Objective):**  
  Specific measurable targets within SLA.
- **SLI (Service Level Indicator):**  
  Metrics to measure SLO fulfillment.

## 169. Disaster Recovery

- **Importance:**  
  Minimize downtime, data loss after disasters.

### Key Terms
- **RTO (Recovery Time Objective):**  
  Maximum acceptable downtime.
- **RPO (Recovery Point Objective):**  
  Maximum acceptable data loss.

### Strategies
- **Backup:** Off-site data storage.
- **Cold Site:** Pre-established infrastructure.
- **Hot Site:** Ready-to-go, up-to-date copy of infrastructure.

## 171. Virtual Machines and Containers

- **Virtual Machines (VMs):**  
  Emulate full hardware systems via hypervisors; isolated, heavier resource use.
- **Containers:**  
  OS-level virtualization; lightweight, share kernel, fast startup.

**Benefits of Containers:**  
- Portability, isolation, agility, efficient resource use.

## 174. OAuth 2.0 and OpenID Connect (OIDC)

### OAuth 2.0
- **Purpose:**  
  Authorization protocol granting limited access without sharing credentials.
- **Entities:**  
  Resource Owner, Client, Authorization Server, Resource Server.
- **Flow:**  
  Client requests access, obtains token after user consent, uses token to access resources.

### OpenID Connect (OIDC)
- **Builds on OAuth 2.0:**  
  Adds authentication layer, providing user identity information.
- **Components:**  
  Relying Party, OpenID Provider, Token Endpoint, UserInfo Endpoint.

## 177. Single Sign-On (SSO)

- **Concept:**  
  One set of credentials grants access to multiple services.
- **Components:**  
  Identity Provider (IdP), Service Provider (SP), Identity Broker.
- **Protocols:**  
  SAML for enterprise-level SSO, OAuth 2.0/OpenID Connect for modern applications.

**Advantages:**  
- User convenience, improved security, reduced IT overhead.

**Disadvantages:**  
- Single password vulnerability, potential slower authentication due to SSO flow.

## 182. Communication Security Protocols

### SSL/TLS/mTLS
- **SSL:** Deprecated protocol for secure communication.
- **TLS:** Successor to SSL; provides encryption, authentication, data integrity.
- **mTLS:** Mutual TLS for bidirectional authentication between client and server.

## Chapter V. System Design Interviews

### Strategies for System Design Interviews
1. **Clarify Requirements:**  
   - Ask about functional, non-functional, and extended requirements.
2. **Estimate and Identify Constraints:**  
   - Scale, traffic patterns, storage, etc.
3. **Data Model Design:**  
   - Define entities, relationships.
4. **API Design:**  
   - Outline API endpoints and interfaces.
5. **High-level Architecture:**  
   - Identify key components (load balancers, databases, caches).
6. **Detailed Design:**  
   - Dive into component specifics, data partitioning, caching, bottleneck resolution.
7. **Identify and Resolve Bottlenecks:**  
   - Look for single points of failure, scalability issues, etc.

### URL Shortener Example (Summary)
- **Functional Requirements:**  
  - Shorten URLs, redirect, expiration.
- **Non-functional Requirements:**  
  - High availability, scalability, efficiency.
- **Estimates:**  
  - Read-heavy load, storage calculations, bandwidth, caching needs.
- **High-level Design:**  
  - Data model with users and URLs, choice of NoSQL, key generation strategies, caching with LRU.
- **Bottlenecks:**  
  - Identify potential single points of failure, load distribution, database load, KGS failures, etc.
~~~
# System Design Summary (Continued)

## Improving Cache Availability
- **Strategies:**
  - Run multiple instances of cache servers.
  - Use load balancers between cache and application servers.
  - Employ cache replication and clustering.
  - Monitor cache health and failover mechanisms.
  - Utilize multiple instances/replicas of distributed cache for redundancy.

*Tip:* Read the engineering blog of the interviewing company to understand their tech stack and critical problems.

---

## URL Shortener Design Recap

### Overview
- **Purpose:** Create short URLs that redirect to long URLs.
- **Key Features:** 
  - Generate unique aliases.
  - Redirect users.
  - Support for expiration.

### Requirements
- **Functional:** URL shortening, redirection, expiration.
- **Non-functional:** High availability, scalability, efficiency.
- **Extended:** Abuse prevention, analytics.

### Estimation Highlights
- **Traffic:** ~40 writes/sec, 4K reads/sec.
- **Storage:** ~6 TB for 10 years.
- **Caching:** Cache 20% of redirection requests (~35 GB/day).

### Data Model
- **Tables:** `users`, `urls` (with indexed `hash` column).

### Database Choice
- Prefer NoSQL (e.g., DynamoDB, Cassandra) due to flexibility and scalability needs.

### API Endpoints
- `createURL(apiKey, originalURL, expiration?)`
- `getURL(apiKey, shortURL)`
- `deleteURL(apiKey, shortURL)`

### High-Level Design Components
- **Key Generation Service (KGS):** Generates unique keys using approaches like Base62 encoding, counters with ZooKeeper, or pre-generated key pools.
- **Caching:** Use LRU policy; update cache on misses.
- **Data Partitioning:** Use consistent hashing and sharding strategies.
- **Security:** API keys, authorization checks.

### Bottleneck Solutions
- Run multiple service instances.
- Load balancing.
- Database replicas.
- Distributed cache replication.

---

## WhatsApp-like Messaging Service Design Recap

### Key Features
- **Functional:** One-on-one and group chats, file sharing.
- **Non-functional:** High availability, low latency, scalability.
- **Extended:** Read receipts, last seen, push notifications.

### Estimation Highlights
- **Users:** 50M DAU.
- **Messages:** 2B/day, 24K RPS.
- **Storage:** 200 GB/day for messages + 10 TB/day for media.

### Data Model
- Tables: `users`, `messages`, `chats`, `users_chats`, `groups`, `users_groups`.

### Core Services
- **User Service:** Authentication, user profiles.
- **Chat Service:** WebSocket connections, real-time messaging.
- **Notification Service:** Push notifications via FCM/APNS.
- **Presence Service:** Track user online/offline status.
- **Media Service:** Handle uploads, storage, and processing.

### Real-time Communication
- Use WebSockets for push model to reduce latency.
- Implement heartbeat for "last seen" status.

### Notifications & Read Receipts
- Use message queues to route notifications.
- Track acknowledgments for delivered/read receipts.

### Video Streaming & Processing (for Netflix design)
- Use CDN, adaptive bitrate streaming, Open Connect model.
- Process uploads through stages: chunking, filtering, transcoding, quality conversion.
- Storage: Use distributed object stores (S3) and CDNs.

---

## Twitter-like Social Media Service Design Recap

### Key Features
- **Functional:** Tweet posting, following, newsfeed, search.
- **Extended:** Retweets, favorites, analytics.

### Estimation Highlights
- **Users:** 200M DAU.
- **Tweets:** 1B/day, 12K RPS.
- **Storage:** ~5.1 TB/day, 19 PB over 10 years.
- **Bandwidth:** ~60 MB/s.

### Data Model
- Tables: `users`, `tweets`, `favorites`, `followers`, `feeds`, `feeds_tweets`.

### Core Services
- **User Service, Tweet Service, Newsfeed Service, Search Service, Media Service, Notification Service, Analytics Service.**

### Newsfeed Generation Strategies
- **Pull Model:** On-demand feed generation.
- **Push Model:** Pre-generate and push tweets to followers.
- **Hybrid Model:** Mix of push/pull based on follower count.

### Ranking & Trending
- Use ranking algorithms (e.g., EdgeRank).
- Apply machine learning for trending topics and personalized recommendations.
- Use Elasticsearch for search functionality.

### Retweets Implementation
- Represent retweet as a new tweet referencing original tweet ID.

---

## Netflix-like Video Streaming Service Design Recap

### Key Features
- **Functional:** Video streaming, uploads, search, comments.
- **Non-functional:** High availability, reliability, scalability.
- **Extended:** Geo-blocking, resume playback, analytics.

### Estimation Highlights
- **Users:** 200M DAU.
- **Storage:** 5 PB/day, ~18,250 PB over 10 years.
- **Bandwidth:** ~58 GB/s.

### Data Model
- Tables: `users`, `videos`, `tags`, `views`, `comments`.

### Core Services
- **User Service, Stream Service, Search Service, Media Service, Analytics Service.**

### Video Processing Pipeline
1. **File Chunker:** Split video into chunks, scene-based.
2. **Content Filter:** ML models to check for violations.
3. **Transcoder:** Convert video to optimized formats.
4. **Quality Conversion:** Generate multiple resolutions.

### Video Streaming
- Use CDN for content delivery.
- Employ adaptive bitrate streaming (e.g., HLS).
- Use geolocation routing for geo-blocking content.

---

## Uber-like Ride-Hailing Service Design Recap

### Key Features
- **Functional (Customers):** View nearby cabs, book rides, track driver location.
- **Functional (Drivers):** Accept/deny rides, view pickup, complete trips.
- **Extended:** Ratings, payment processing, analytics.

### Estimation Highlights
- **Users:** 100M daily, 1M drivers.
- **Rides:** 10M/day.
- **RPS:** ~12K.
- **Storage:** ~400 GB/day, ~1.4 PB over 10 years.
- **Bandwidth:** ~5 MB/s.

### Data Model
- Tables: `customers`, `drivers`, `trips`, `cabs`, `ratings`, `payments`.

### Core Services
- **Customer Service, Driver Service, Ride Service, Trip Service, Payment Service, Notification Service, Analytics Service.**

### Real-time Features
- **Location Tracking:** Use WebSockets for live updates.
- **Ride Matching:** Utilize geohashing or quadtrees to find nearby drivers.
- **Surge Pricing:** Dynamically adjust prices based on demand.
- **Payments:** Integrate third-party processors (Stripe/PayPal).
- **Notifications:** Use message brokers for push notifications.

### Data Handling & Partitioning
- Shard data using consistent hashing.
- Use caching (LRU policy) for recent driver/customer locations.
- Employ service discovery, load balancers, and multiple replicas for resilience.

---

## Appendix: Next Steps & Resources
- Follow engineering blogs of tech companies (Netflix, Google, AWS, etc.).
- Explore additional resources:
  - Distributed Systems by Martin Kleppmann
  - Grokking the System Design Interview
  - Microservices by Chris Richardson
  - Serverless computing, Kubernetes, etc.

*Note:* This summary encapsulates key design considerations, requirements, and strategies across multiple system design scenarios, highlighting scalability, reliability, and resilience strategies.
~~~

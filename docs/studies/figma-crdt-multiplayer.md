# 🔥 Figma – Rewriting Multiplayer Engine With a Custom CRDT

## 1. Background

Figma is a real-time collaborative design tool where:

- Many users can edit the same document simultaneously.
- Every change must propagate quickly to all connected clients.
- The system must handle conflicts and offline edits without losing data.
- Documents can be very large, with thousands of objects and layers.

Early on, Figma’s collaboration engine was based on traditional Operational Transformation (OT) techniques, similar to those used in text editors and document tools.

## 2. Problem

From all.md:

- Problem: Real-time collaboration was hitting bandwidth + CPU limits.
- Cause: Their OT-based system required too many transformations.
- Solution:
  - Built a custom CRDT optimized for graphics objects.
  - Implemented binary deltas instead of JSON.
  - Used sharded document state to isolate hot areas.
- Impact: Real-time sync became smooth even with huge documents.

In practice:

- As documents grew and more collaborators joined, CPU usage and network bandwidth both spiked.
- The number of transformations required to reconcile edits became large.
- Latency increased, especially when many edits occurred in different parts of the document.

## 3. Root Causes

### 3.1 OT complexity on rich, non-linear data

Operational Transformation works well for:

- Linear text documents (strings of characters).
- Simple insertion/deletion operations.

Figma’s data model is different:

- Hierarchical scene graph (frames, groups, shapes, vectors).
- Properties like position, color, effects, constraints.
- Many independent objects changing concurrently.

OT on this model:

- Required many transformation steps when operations interleaved.
- Became complex and costly as operations and collaborators increased.

### 3.2 Bandwidth-heavy JSON updates

The old system:

- Used JSON-based messages to describe changes.
- Sometimes sent more data than necessary (full object or large diffs).
- Repeated keys and structure added overhead.

Under heavy editing:

- The volume of JSON deltas grew large.
- Clients and servers spent significant CPU time encoding/decoding JSON.

### 3.3 Global contention on shared document state

If document changes were coordinated in a single global structure:

- Edits in one part of the document could affect how operations elsewhere were processed or ordered.
- Locking or coordination at the document level increased contention.
- Hot spots in the document (e.g., active artboards) stressed the same structures.

## 4. Architecture Before (Simplified)

Conceptually, the original engine looked like:

```text
Clients
  │  (OT operations over JSON)
  ▼
┌─────────────────────────────┐
│ Collaboration Server        │
│  - OT engine on doc state   │
│  - JSON-based operations    │
└───────────┬─────────────────┘
            │
            ▼
        Document Store
```

Characteristics:

- Operations expressed in terms of OT on shared state.
- JSON-based payloads for operations and state updates.
- The OT engine had to transform operations frequently, especially under concurrency.

## 5. Architecture After (Optimized)

Figma moved to a custom CRDT-based engine tailored to their graphics scene graph, with efficient binary deltas and sharded state:

```text
Clients
  │  (CRDT ops as compact binary deltas)
  ▼
┌───────────────────────────────────┐
│ Multiplayer Engine (CRDT)        │
│  - Graphics-object CRDT          │
│  - Binary deltas                 │
│  - Sharded document regions      │
└───────────┬──────────────────────┘
            │
            ▼
        Document Store
   (CRDT-backed, region-sharded)
```

Key ideas:

- Use CRDTs to make operations commutative and convergent without OT transforms.
- Represent edits as binary deltas optimized for graphics objects.
- Shard document state so different regions can be updated independently.

## 6. How They Implemented the Fix (Step by Step)

### 6.1 Design a CRDT for graphics objects

Instead of treating the document as a simple list of operations, the new engine:

- Models each object (frame, shape, vector) as an entity with CRDT-managed fields.
- Uses CRDT types that support:

  - Last-writer-wins for some properties.
  - Merged sets/maps for collections of objects.
  - Order-preserving structures for layers and z-indices when needed.

Benefits:

- Concurrent edits to different properties or objects can merge automatically.
- Conflicts resolve deterministically without requiring OT transforms.
- Offline changes can be merged when the client reconnects.

### 6.2 Replace JSON messages with binary deltas

To reduce bandwidth and CPU:

- Define a compact binary encoding for CRDT operations:
  - Type tags.
  - Object IDs.
  - Property IDs.
  - Values (encoded efficiently).
- Send only the minimal information needed to describe the change.

For example, instead of:

```json
{
  "op": "update",
  "nodeId": "123",
  "property": "x",
  "value": 100
}
```

A binary delta might be:

- A few bytes encoding (operation type, object ID, property ID, new value), without repeated keys or JSON structure.

Impact:

- Smaller messages over the network.
- Lower CPU cost for serialization and parsing.
- Better performance on slow or high-latency connections.

### 6.3 Shard document state into hot regions

Documents can be logically partitioned:

- By page or artboard.
- By groups of layers or components.
- By spatial regions.

The engine:

- Splits document state into shards or regions.
- Manages each shard’s CRDT state separately.
- Routes operations to the relevant shard based on object location or grouping.

Effects:

- Edits in one area of the document do not lock or heavily impact other areas.
- Servers can distribute shards across processes or machines.
- Caching and replication can focus on hot regions where users are working.

### 6.4 Optimize client-side application of deltas

On the client:

- Apply CRDT operations incrementally to local state.
- Use efficient data structures so that UI updates are cheap:

  - Incremental scene graph updates.
  - Dirty marking of only affected nodes.
  - Offscreen preparation for rendering.

- Avoid re-rendering the entire document on every delta.

Benefits:

- Smooth cursor and object motion even under many concurrent edits.
- Reduced CPU and GPU usage on client machines.

### 6.5 Ensure convergence, ordering, and resilience

CRDT-based design ensures:

- Eventual consistency: all replicas converge given the same set of operations.
- Commutativity: operation application order doesn’t affect final state (within CRDT rules).
- Resilience to dropped or reordered messages.

The system incorporates:

- Versioning and operation IDs to track progress.
- Efficient snapshotting and catch-up logic for clients that reconnect or fall behind.

## 7. Performance Results

From the original summary:

- Impact: Real-time sync became smooth even with huge documents.

In practice, this means:

- Lower bandwidth usage per client.
- Reduced CPU usage both on server and client for each edit.
- More predictable performance as the number of collaborators and document size grows.
- Less stutter and lag when multiple people edit large, complex designs concurrently.

## 8. Lessons and Reusable Patterns

### 8.1 Choose collaboration primitives that fit your data

- OT is well-suited to linear text but can be complex for rich, structured data.
- For hierarchical or graph-structured data (like design documents), CRDTs tailored to the domain can be simpler and more efficient.

### 8.2 Use binary protocols for high-frequency updates

- For real-time systems with many small messages, JSON overhead is significant.
- Compact binary encodings reduce both bandwidth and CPU overhead.

### 8.3 Shard state along natural boundaries

- Partition documents into regions that align with how users work (pages, boards, components).
- Sharding reduces contention and allows independent scaling.

### 8.4 Optimize both server and client paths

- Real-time collaboration performance depends on both ends:
  - Server-side propagation and merge.
  - Client-side application and rendering.
- Use incremental updates and avoid full recomputation when possible.

### 8.5 Prefer conflict-free designs for offline and async edits

- CRDTs allow edits to be made offline and merged later without complex conflict resolution logic.
- This is especially valuable in collaborative tools where connectivity is not always perfect.

These patterns generalize to other real-time collaborative apps: code editors, whiteboards, note-taking tools, and any system where many users mutate shared structured data concurrently.

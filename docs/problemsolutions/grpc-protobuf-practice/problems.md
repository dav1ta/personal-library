# Problem 1: Schema Evolution & Backward Compatibility

**Real-World Scenario:** 
A `UserProfile` service is live with thousands of mobile clients. Product requirements change: we need to add a `middle_name` field and we realized `email` should be more generic, so we want to rename it to `contact_info`. We must deploy these changes without breaking older mobile apps that haven't updated yet.

**System Design Pattern:** 
Protobuf Field Stability & Reserved Tags

**Solution Logic:** 
We will define an initial `User` message. Then, we will "evolve" it by adding a new field and renaming an existing one, while strictly adhering to Protobuf rules (never changing field tags). We will demonstrate that an old client can still read the new message (ignoring new fields) and a new client can read old messages (handling missing fields gracefully).

# Problem 2: Efficient Large Dataset Retrieval

**Real-World Scenario:** 
An `Analytics` dashboard needs to fetch 50,000 raw event logs for a specific user session. A simple Unary RPC (`GetLogs`) attempts to load all 50k records into memory and serialize them into one massive response, causing OOM (Out of Memory) kills on the server and timeouts on the client.

**System Design Pattern:** 
Server-Side Streaming

**Solution Logic:** 
Instead of returning `GetLogsResponse` containing a list of logs, we will define `rpc GetLogs returns (stream LogEntry)`. The server will stream records one by one (or in small batches), allowing the client to process/display them immediately without buffering the entire dataset.

# Problem 3: Rich Domain Error Handling

**Real-World Scenario:** 
A `Transaction` service fails a transfer. A standard gRPC `INTERNAL` or `INVALID_ARGUMENT` code isn't enough. The client needs to know *why* it failed (e.g., "Limit Exceeded" or "Account Frozen") and potentially metadata (e.g., "Current Limit: $500").

**System Design Pattern:** 
Rich Error Model (`google.rpc.Status`)

**Solution Logic:** 
We will avoid mapping everything to simple HTTP-like status codes. Instead, we will use the `google.rpc.Status` model to attach strongly-typed error details (like a `QuotaFailure` or custom `TransactionError` message) to the gRPC error response, allowing the client to programmatically handle specific failure scenarios.

Next: [High Throughput Logger](../high-throughput-logger/problems.md)

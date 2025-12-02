# Problem 2: Async Fan-Out Publishing

**Real-World Scenario:**
An event occurs in your system (e.g., "UserRegistered"). You need to perform multiple actions with this data:
1.  Save to Primary DB (Postgres).
2.  Index in Search Engine (Elasticsearch).
3.  Send Welcome Email (SMTP).

Doing this synchronously in the API handler is slow and brittle. If the Email service hangs, the User Registration fails. You need to decouple the "Event" from the "Handlers" and ensure all handlers run reliably.

**System Design Pattern:**
**Fan-Out (Observer Pattern)** + **Async Producer-Consumer**.

**Solution Logic:**
1.  **Abstraction**: Define a `Publisher` interface (Abstract Base Class). All destinations (DB, Search, Email) implement this.
2.  **Composite**: Create a `FanOutPublisher` that holds a list of `Publisher` instances. When it receives a message, it broadcasts it to all children.
3.  **Decoupling**: The Main App (Producer) pushes data to an `asyncio.Queue`. It doesn't know about the publishers.
4.  **Worker**: A background consumer reads from the Queue and calls the `FanOutPublisher`.
5.  **Error Isolation**: If one publisher fails (e.g., Email is down), it shouldn't crash the others (DB/Search).

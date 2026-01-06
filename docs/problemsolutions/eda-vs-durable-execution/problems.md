# eda-vs-durable-execution — Problems & Solutions

> Each entry: problem with a concrete example → solution / pattern / good practice.

---

At-least-once Delivery (Duplicates)
- Problem (example): Consumer crashes after writing DB state but before ack; queue redelivers → duplicate process.
- Solution / Good practice: Make handlers idempotent via keys/state checks; store processed ids; use transactional outbox/inbox where possible.

Manual Retries, Backoff, and DLQ
- Problem (example): Producer publish fails intermittently; naive tight loops create retry storms.
- Solution / Good practice: Exponential backoff with jitter; cap attempts; divert to DLQ and surface for inspection.

Scattered Logic Across Services
- Problem (example): Validation in one service, storage in another, clearing later via cron; state spread across systems.
- Solution / Good practice: Durable workflows centralize business steps (validate → record → wait → clear) with the platform managing retries/timers.

Determinism in Workflows
- Problem (example): Random/time/syscalls inside workflow cause non-deterministic replays.
- Solution / Good practice: Keep workflow code pure and deterministic; perform side effects in Activities; the engine records results and replays.

Timers and Waiting
- Problem (example): Scheduling delays via queues/cron is ad-hoc and consumes infra.
- Solution / Good practice: Durable timers (sleep) pause without compute; workflow resumes reliably.

Fair Comparison Scenario
- Both flows perform: validate → record to DB → wait (timer) → clear.
- Failures injected: publish flake, DB hiccup, crash after DB write.

Next: [gRPC Protobuf Practice](../grpc-protobuf-practice/problems.md)

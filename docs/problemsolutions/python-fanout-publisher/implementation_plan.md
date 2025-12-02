# Implementation Plan: Async Fan-Out Publisher

## Goal
Create a clean, extensible Python service that consumes messages from a queue and publishes them to multiple destinations simultaneously.

## Tech Stack
-   **Language**: Python 3.10+
-   **Concurrency**: `asyncio`
-   **Design Patterns**: Strategy, Composite, Producer-Consumer

## Proposed Architecture

### 1. The Interface (`interfaces.py`)
-   **`Publisher` (ABC)**: Defines the contract `async def publish(self, data: dict) -> None`.
-   **Why ABC?**: Enforces structure. Every new destination MUST implement `publish`.

### 2. The Implementations (`publishers.py`)
-   **`LogPublisher`**: Prints to stdout (Mock).
-   **`DatabasePublisher`**: Simulates writing to a DB (sleeps).
-   **`MetricsPublisher`**: Simulates sending stats to Datadog (sleeps).
-   **`FanOutPublisher`**:
    -   Takes `List[Publisher]` in `__init__`.
    -   In `publish()`, uses `asyncio.gather()` to run all children in parallel.
    -   Handles exceptions (so one failure doesn't stop others).

### 3. The Consumer (`worker.py`)
-   **`Consumer` Class**:
    -   Holds the `asyncio.Queue`.
    -   Holds the `FanOutPublisher`.
    -   **`start()`**: Infinite loop popping from queue and calling `publisher.publish()`.

### 4. The Main App (`main.py`)
-   Setup Dependency Injection (assemble the classes).
-   Start the Worker.
-   Simulate a Producer pushing messages to the queue.

## Directory Structure
```
python-fanout-publisher/
├── interfaces.py   # Abstract Base Classes
├── publishers.py   # Concrete Implementations
├── worker.py       # Queue Consumer
├── main.py         # Entry Point
├── problems.md     # Problem Definition
└── implementation_plan.md
```

## Verification Plan
1.  Run `python main.py`.
2.  Observe logs:
    -   Producer pushes "Event 1".
    -   **Simultaneously** (approx same timestamp):
        -   [DB] Saving Event 1...
        -   [Log] Event 1...
        -   [Metrics] Sending Event 1...
3.  Verify that the Producer does NOT wait for the Publishers (Queue decoupling).

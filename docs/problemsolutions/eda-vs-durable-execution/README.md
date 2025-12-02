# eda-vs-durable-execution

Minimal, runnable Python examples contrasting Event-Driven Architecture (EDA) with Durable Execution (workflow-style).

Goals
- Keep it small and stdlib-only; no external deps.
- Make failure modes visible (retries, duplicates, crash-before-ack).
- Show how Durable Execution centralizes logic and abstracts retries/state.

Run

EDA demo
```
python3 src/eda_demo.py
```

Durable Execution demo (pseudo-framework)
```
python3 src/durable_demo.py
```

What to look for
- EDA: manual retry/backoff, potential duplicate delivery, idempotent processing required.
- Durable: sequential workflow code; framework wrapper retries activities and records step completion; resume after crash continues from last durable step.

See `problems.md` for problem→solution pairs and references.


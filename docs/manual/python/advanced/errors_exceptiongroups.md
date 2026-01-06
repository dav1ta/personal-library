# ExceptionGroup and `except*` (3.11+)

Handle multiple concurrent errors cleanly, especially with asyncio TaskGroup and parallel executors.

## Raising and Handling Groups

```python
def multi_fail():
    excs = [ValueError("bad value"), KeyError("missing"), RuntimeError("boom")]
    raise ExceptionGroup("batch errors", excs)

try:
    multi_fail()
except* ValueError as eg:
    for e in eg.exceptions:
        print("value error:", e)
except* KeyError as eg:
    ...
except* Exception as eg:
    # remaining grouped by type
    ...
```

`except*` partitions the group by matching types; unmatched exceptions propagate.

---

## With asyncio TaskGroup

```python
import asyncio

async def ok():
    return 1

async def boom():
    raise ValueError("x")

async def main():
    try:
        async with asyncio.TaskGroup() as tg:
            tg.create_task(ok())
            tg.create_task(boom())
            tg.create_task(boom())
    except* ValueError as eg:
        # both ValueErrors handled here; others would bubble
        print(len(eg.exceptions))

asyncio.run(main())
```

Design tips:
- Group errors at boundaries and report with structure; don’t flatten into strings.
- Combine with logging to emit one summary with counts per type.

Next: [Import Hooks & AST](import_hooks_ast.md)

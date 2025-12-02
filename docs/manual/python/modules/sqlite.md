# SQLite with `sqlite3`

## Connect and Row Factory

```python
import sqlite3

conn = sqlite3.connect("app.db")
conn.row_factory = sqlite3.Row  # dict-like access
```

---

## Schema and Transactions

```python
with conn:  # transaction
    conn.execute("""
        CREATE TABLE IF NOT EXISTS users (
            id INTEGER PRIMARY KEY,
            name TEXT NOT NULL,
            age INTEGER
        )
    """)
```

Using `with conn:` wraps statements in a transaction that commits on success and rolls back on exception.

---

## Parameterized Queries (No f-strings)

```python
with conn:
    conn.execute("INSERT INTO users(name, age) VALUES(?, ?)", ("alice", 30))

cur = conn.execute("SELECT * FROM users WHERE age >= ?", (21,))
for row in cur:
    print(dict(row))
```

---

## Bulk Inserts with `executemany`

```python
users = [("bob", 28), ("carol", 33)]
with conn:
    conn.executemany("INSERT INTO users(name, age) VALUES(?, ?)", users)
```

---

## Pragmas and WAL Mode

```python
conn.execute("PRAGMA foreign_keys = ON")
conn.execute("PRAGMA journal_mode = WAL")
```

WAL improves concurrency for readers; set pragmas early after connect.

---

## User-Defined Functions

```python
def py_upper(s: str) -> str:
    return s.upper()

conn.create_function("py_upper", 1, py_upper)
print(conn.execute("SELECT py_upper(name) FROM users").fetchone()[0])
```

---

## Tips

- Always use `?` parameters; never interpolate SQL.
- Keep one connection per thread; enable `check_same_thread=False` only if you manage access.
- Use `Row` factory for readable code and JSON serialization.


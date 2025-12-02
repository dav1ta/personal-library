# Database Patterns (General)

Concepts that apply across DB drivers and ORMs.

## Parameterized Queries

- Use driver placeholders (e.g., `?` for sqlite3, `%s` for psycopg/pymysql).
- Never build SQL via string concatenation or f-strings.

```python
sql = "SELECT * FROM users WHERE email = ?"
conn.execute(sql, (email,))
```

---

## Transactions

```python
with conn:  # commits on success, rollbacks on exception
    conn.execute("UPDATE accounts SET balance = balance - ? WHERE id=?", (100, 1))
    conn.execute("UPDATE accounts SET balance = balance + ? WHERE id=?", (100, 2))
```

Keep transactions small; avoid long-running holds.

---

## Migrations

- Keep schema changes in versioned migrations (Alembic for SQLAlchemy, etc.).
- Never modify production schema manually; review and test migrations.

---

## Connection Management

- Use pools for external DBs (e.g., SQLAlchemy engine) to reuse connections.
- Close cursors promptly; prefer context managers when supported.

---

## Data Access Layers

- Keep SQL in a data-access module; avoid scattering queries.
- Return typed objects or dicts; decouple storage from domain logic.

---

## Testing

- Use in-memory sqlite or test containers for isolated tests.
- Seed fixtures; wrap tests in transactions truncated between tests.


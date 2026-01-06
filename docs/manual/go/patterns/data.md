# Data Access Patterns

Practical patterns for `database/sql` usage.

## Open and Configure Pool
```go
db, err := sql.Open("postgres", dsn)
if err != nil {
    return err
}
db.SetMaxOpenConns(20)
db.SetMaxIdleConns(5)
db.SetConnMaxLifetime(30 * time.Minute)

ctx, cancel := context.WithTimeout(context.Background(), 3*time.Second)
defer cancel()
if err := db.PingContext(ctx); err != nil {
    return err
}
```

## Query Row
```go
var u User
err := db.QueryRowContext(ctx, "select id, name from users where id=$1", id).
    Scan(&u.ID, &u.Name)
if errors.Is(err, sql.ErrNoRows) {
    return nil
}
if err != nil {
    return err
}
```

## Query Many Rows
```go
rows, err := db.QueryContext(ctx, "select id, name from users where active=$1", true)
if err != nil {
    return err
}
defer rows.Close()

for rows.Next() {
    var u User
    if err := rows.Scan(&u.ID, &u.Name); err != nil {
        return err
    }
}
if err := rows.Err(); err != nil {
    return err
}
```

## Transactions
```go
tx, err := db.BeginTx(ctx, nil)
if err != nil {
    return err
}
defer tx.Rollback()

if _, err := tx.ExecContext(ctx, "update users set name=$1 where id=$2", name, id); err != nil {
    return err
}
return tx.Commit()
```

## Tips
- Always use context-aware methods.
- Close rows quickly; do not defer inside loops.
- Handle `sql.ErrNoRows` explicitly.

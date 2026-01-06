# Docker Compose (Minimal Example)

```yaml
services:
  api:
    build: .
    ports:
      - "8000:8000"
    environment:
      DATABASE_URL: postgres://app:app@db:5432/app
    depends_on:
      - db

  db:
    image: postgres:16
    environment:
      POSTGRES_USER: app
      POSTGRES_PASSWORD: app
      POSTGRES_DB: app
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

## Notes
- If your tooling expects a `version` field, add one; otherwise omit.
- Use `depends_on` for start order, not readiness.
- Add healthchecks if your app depends on DB readiness.

Next: [Docker](docker.md)

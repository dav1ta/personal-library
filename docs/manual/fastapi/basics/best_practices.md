# FastAPI Best Practices (Concise)

## Structure
- Keep `main.py` thin; move logic into `services/` and `routers/`.
- Group routes by feature, not by HTTP method.
- Use an `api/v1` prefix for versioned APIs.

```
app/
  main.py
  api/
    v1/
      router.py
      users.py
  core/
    config.py
  db/
    session.py
  schemas/
    user.py
  services/
    user_service.py
```

## Settings and Config
Use `pydantic-settings` for typed config; keep secrets in env.

```python
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    api_v1_prefix: str = "/v1"
    database_url: str

    class Config:
        env_file = ".env"
```

## Dependency Injection
- Keep dependencies small and single-purpose.
- Inject DB sessions and auth via `Depends`.
- Use dependency overrides for tests.

## DB Sessions
```python
from contextlib import contextmanager

@contextmanager
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

## Error Handling
- Use `HTTPException` for API errors.
- Add exception handlers for consistent error shapes.

## Security
- Never hardcode secrets.
- Use proper password hashing (bcrypt/argon2).
- Validate auth scopes inside dependencies.

## Performance
- Paginate list endpoints.
- Use response caching where safe.
- Avoid N+1 DB queries.

## Testing
- Use `TestClient` for route tests.
- Prefer integration tests for real DB paths.

Next: [Troubleshooting](troubleshooting.md)

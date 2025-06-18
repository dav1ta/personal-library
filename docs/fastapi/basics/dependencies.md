# Dependencies in FastAPI

FastAPI has a powerful dependency injection system that allows you to share common logic across your application.

## Basic Dependencies

Here's a simple example of using dependencies:

```python
from fastapi import FastAPI, Depends, HTTPException
from typing import Optional

app = FastAPI()

async def common_parameters(q: Optional[str] = None, skip: int = 0, limit: int = 100):
    return {"q": q, "skip": skip, "limit": limit}

@app.get("/items/")
async def read_items(commons: dict = Depends(common_parameters)):
    return commons
```

## Class-based Dependencies

You can also use classes as dependencies:

```python
from fastapi import FastAPI, Depends
from typing import Optional

app = FastAPI()

class CommonQueryParams:
    def __init__(self, q: Optional[str] = None, skip: int = 0, limit: int = 100):
        self.q = q
        self.skip = skip
        self.limit = limit

@app.get("/items/")
async def read_items(commons: CommonQueryParams = Depends()):
    return commons
```

## Nested Dependencies

Dependencies can be nested:

```python
from fastapi import FastAPI, Depends, HTTPException
from typing import Optional

app = FastAPI()

async def verify_token(token: str):
    if token != "fake-super-secret-token":
        raise HTTPException(status_code=400, detail="Invalid token")
    return token

async def get_current_user(token: str = Depends(verify_token)):
    return {"username": "fakeuser", "token": token}

@app.get("/users/me")
async def read_users_me(current_user: dict = Depends(get_current_user)):
    return current_user
```

## Path Operation Dependencies

You can add dependencies to specific path operations:

```python
from fastapi import FastAPI, Depends, HTTPException
from typing import Optional

app = FastAPI()

async def verify_key(key: str):
    if key != "fake-super-secret-key":
        raise HTTPException(status_code=400, detail="Invalid key")
    return key

@app.get("/items/", dependencies=[Depends(verify_key)])
async def read_items():
    return [{"item": "Foo"}, {"item": "Bar"}]
```

## Best Practices

1. **Reusability**
   - Create dependencies that can be reused across multiple endpoints
   - Keep dependencies focused on a single responsibility

2. **Error Handling**
   - Use proper exception handling in dependencies
   - Return meaningful error messages

3. **Type Safety**
   - Use type hints for better IDE support
   - Leverage Pydantic models for complex data structures

4. **Testing**
   - Make dependencies easy to test
   - Consider using dependency overrides for testing

## Common Use Cases

- Authentication and authorization
- Database session management
- Request validation
- Rate limiting
- Logging and monitoring
- Configuration management

## Next Steps

- Learn about Security Features
- Understand [Advanced Patterns](../advanced/crud_patterns.md)
- Explore [Best Practices](best_practices.md) 
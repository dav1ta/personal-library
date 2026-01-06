# JWT Auth (FastAPI, Concise)

Minimal JWT setup with refresh tokens and dependency-based auth.

## Dependencies
```bash
pip install python-jose[cryptography] passlib[bcrypt] python-multipart
```

## Settings
```python
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    secret_key: str
    refresh_secret_key: str
    algorithm: str = "HS256"
    access_minutes: int = 15
    refresh_days: int = 7
```

## Hashing
```python
from passlib.context import CryptContext
pwd = CryptContext(schemes=["bcrypt"], deprecated="auto")

def hash_pw(p: str) -> str: return pwd.hash(p)
def verify_pw(p: str, h: str) -> bool: return pwd.verify(p, h)
```

## Tokens
```python
from jose import jwt
from datetime import datetime, timedelta

def make_token(data: dict, secret: str, minutes: int):
    exp = datetime.utcnow() + timedelta(minutes=minutes)
    return jwt.encode({**data, "exp": exp}, secret, algorithm=settings.algorithm)
```

## Auth Dependency
```python
from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer

oauth2 = OAuth2PasswordBearer(tokenUrl="token")

async def current_user(token: str = Depends(oauth2)):
    try:
        payload = jwt.decode(token, settings.secret_key, algorithms=[settings.algorithm])
    except Exception:
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Invalid token")
    return await get_user(payload.get("sub"))
```

## Refresh Flow
- Access tokens are short-lived.
- Refresh tokens rotate (issue a new refresh token on use).
- Store refresh tokens securely (httpOnly cookie or DB allowlist).

## Security Notes
- Always use HTTPS.
- Use strong secrets and rotate keys.
- Enforce auth scopes/roles in dependencies.

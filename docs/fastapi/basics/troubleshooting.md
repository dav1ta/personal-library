# FastAPI Troubleshooting Guide

This guide covers common issues and their solutions when working with FastAPI.

## Common Issues

### 1. CORS Issues

**Problem**: Browser blocks requests due to CORS policy.

**Solution**:
```python
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],  # In production, replace with specific origins
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

### 2. Database Connection Issues

**Problem**: Database connection errors or timeouts.

**Solution**:
```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

engine = create_engine(
    DATABASE_URL,
    pool_pre_ping=True,  # Enable connection health checks
    pool_size=5,         # Adjust based on your needs
    max_overflow=10
)
```

### 3. Authentication Problems

**Problem**: JWT token validation fails.

**Solution**:
```python
from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

async def get_current_user(token: str = Depends(oauth2_scheme)):
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        user = get_user(payload.get("sub"))
        if user is None:
            raise HTTPException(
                status_code=status.HTTP_401_UNAUTHORIZED,
                detail="Invalid authentication credentials"
            )
        return user
    except JWTError:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid authentication credentials"
        )
```

### 4. Performance Issues

**Problem**: Slow response times.

**Solutions**:
1. Enable response compression:
```python
from fastapi.middleware.gzip import GZipMiddleware

app.add_middleware(GZipMiddleware, minimum_size=1000)
```

2. Use connection pooling:
```python
from databases import Database

database = Database(DATABASE_URL, min_size=5, max_size=20)
```

### 5. File Upload Issues

**Problem**: Large file uploads fail.

**Solution**:
```python
from fastapi import File, UploadFile
from starlette.responses import StreamingResponse

@app.post("/upload/")
async def upload_file(file: UploadFile = File(...)):
    try:
        contents = await file.read()
        # Process file contents
        return {"filename": file.filename}
    except Exception as e:
        raise HTTPException(
            status_code=400,
            detail=f"Error processing file: {str(e)}"
        )
```

### 6. Validation Errors

**Problem**: Pydantic validation errors.

**Solution**:
```python
from fastapi import HTTPException
from pydantic import ValidationError

@app.exception_handler(ValidationError)
async def validation_exception_handler(request, exc):
    return JSONResponse(
        status_code=422,
        content={"detail": exc.errors()}
    )
```

### 7. Async Issues

**Problem**: Blocking operations in async endpoints.

**Solution**:
```python
from fastapi import BackgroundTasks

@app.post("/send-notification/{email}")
async def send_notification(
    email: str, background_tasks: BackgroundTasks
):
    background_tasks.add_task(send_email, email)
    return {"message": "Notification sent in the background"}
```

### 8. Memory Issues

**Problem**: High memory usage.

**Solutions**:
1. Use streaming responses:
```python
from fastapi.responses import StreamingResponse

@app.get("/stream")
async def stream_response():
    async def generate():
        for i in range(1000):
            yield f"data: {i}\n\n"
            await asyncio.sleep(0.1)
    
    return StreamingResponse(generate(), media_type="text/event-stream")
```

2. Implement pagination:
```python
from fastapi import Query

@app.get("/items/")
async def read_items(
    skip: int = Query(0, ge=0),
    limit: int = Query(10, ge=1, le=100)
):
    return {"skip": skip, "limit": limit}
```

## Debugging Tips

1. **Enable Debug Mode**
```python
import uvicorn

if __name__ == "__main__":
    uvicorn.run("main:app", host="0.0.0.0", port=8000, reload=True)
```

2. **Use Logging**
```python
import logging

logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)

@app.get("/")
async def root():
    logger.debug("Processing root request")
    return {"message": "Hello World"}
```

3. **Request/Response Inspection**
```python
from fastapi import Request

@app.middleware("http")
async def log_requests(request: Request, call_next):
    logger.info(f"Request: {request.method} {request.url}")
    response = await call_next(request)
    logger.info(f"Response: {response.status_code}")
    return response
```

## Next Steps

- Learn about [Best Practices](best_practices.md)
- Understand Security Features
- Explore Performance Optimization 
# Getting Started with FastAPI

Minimal setup for a working API.

## Install
```bash
pip install fastapi
pip install "uvicorn[standard]"
```

## Minimal App
```python
from fastapi import FastAPI
from typing import Optional

app = FastAPI()

@app.get("/")
async def root():
    return {"ok": True}

@app.get("/items/{item_id}")
async def read_item(item_id: int, q: Optional[str] = None):
    return {"item_id": item_id, "q": q}
```

## Run
```bash
uvicorn main:app --reload
```

## Next Steps
- [CRUD Operations](crud_operations.md)
- [Dependencies](dependencies.md)
- [Best Practices](best_practices.md)

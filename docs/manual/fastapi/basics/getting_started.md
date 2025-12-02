# Getting Started with FastAPI

FastAPI is a modern, fast (high-performance) web framework for building APIs with Python 3.7+ based on standard Python type hints.

## Installation

```bash
pip install fastapi
pip install "uvicorn[standard]"
```

## Basic Example

Here's a simple FastAPI application:

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def root():
    return {"message": "Hello World"}

@app.get("/items/{item_id}")
async def read_item(item_id: int):
    return {"item_id": item_id}
```

## Running the Application

```bash
uvicorn main:app --reload
```

## Key Features

- Fast: Very high performance, on par with NodeJS and Go
- Fast to code: Increase the speed to develop features by about 200% to 300%
- Fewer bugs: Reduce about 40% of human-induced errors
- Intuitive: Great editor support. Completion everywhere. Less time debugging
- Easy: Designed to be easy to use and learn. Less time reading docs
- Short: Minimize code duplication. Multiple features from each parameter declaration
- Robust: Get production-ready code. With automatic interactive documentation
- Standards-based: Based on (and fully compatible with) OpenAPI and JSON Schema

## Next Steps

- Learn about [CRUD Operations](crud_operations.md)
- Understand [Dependencies](dependencies.md)
- Explore [Best Practices](best_practices.md) 
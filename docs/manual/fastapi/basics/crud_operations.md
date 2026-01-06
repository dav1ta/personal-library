# CRUD Operations (FastAPI)

Minimal CRUD example with clear patterns. Replace in-memory storage with a DB in real apps.

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()
items: dict[int, "Item"] = {}
next_id = 1

class Item(BaseModel):
    name: str
    price: float

@app.post("/items")
def create_item(item: Item):
    global next_id
    items[next_id] = item
    next_id += 1
    return {"id": next_id - 1, **item.dict()}

@app.get("/items/{item_id}")
def read_item(item_id: int):
    if item_id not in items:
        raise HTTPException(404, "not found")
    return {"id": item_id, **items[item_id].dict()}

@app.put("/items/{item_id}")
def update_item(item_id: int, item: Item):
    if item_id not in items:
        raise HTTPException(404, "not found")
    items[item_id] = item
    return {"id": item_id, **item.dict()}

@app.delete("/items/{item_id}")
def delete_item(item_id: int):
    if item_id not in items:
        raise HTTPException(404, "not found")
    items.pop(item_id)
    return {"ok": True}
```

## Notes
- Use Pydantic models for validation.
- Use dependency-injected DB sessions for real storage.

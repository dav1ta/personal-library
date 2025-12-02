# CRUD Operations in FastAPI

CRUD (Create, Read, Update, Delete) operations are fundamental to any API. FastAPI makes implementing these operations straightforward and type-safe.

## Basic CRUD Example

Here's a complete example of CRUD operations for a simple item management system:

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import List, Optional

app = FastAPI()

# Pydantic model for data validation
class Item(BaseModel):
    id: Optional[int] = None
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None

# In-memory database
items = []
item_id_counter = 1

# Create
@app.post("/items/", response_model=Item)
async def create_item(item: Item):
    global item_id_counter
    item.id = item_id_counter
    item_id_counter += 1
    items.append(item)
    return item

# Read
@app.get("/items/", response_model=List[Item])
async def read_items():
    return items

@app.get("/items/{item_id}", response_model=Item)
async def read_item(item_id: int):
    for item in items:
        if item.id == item_id:
            return item
    raise HTTPException(status_code=404, detail="Item not found")

# Update
@app.put("/items/{item_id}", response_model=Item)
async def update_item(item_id: int, item: Item):
    for i, stored_item in enumerate(items):
        if stored_item.id == item_id:
            item.id = item_id
            items[i] = item
            return item
    raise HTTPException(status_code=404, detail="Item not found")

# Delete
@app.delete("/items/{item_id}")
async def delete_item(item_id: int):
    for i, item in enumerate(items):
        if item.id == item_id:
            items.pop(i)
            return {"message": "Item deleted successfully"}
    raise HTTPException(status_code=404, detail="Item not found")
```

## Key Points

1. **Create (POST)**
   - Use `@app.post()` decorator
   - Accept data in request body
   - Validate data using Pydantic models
   - Return created item

2. **Read (GET)**
   - Use `@app.get()` decorator
   - Can return single item or list
   - Handle not found cases

3. **Update (PUT)**
   - Use `@app.put()` decorator
   - Update existing item
   - Validate new data
   - Return updated item

4. **Delete (DELETE)**
   - Use `@app.delete()` decorator
   - Remove item from storage
   - Return success message

## Best Practices

- Always use Pydantic models for data validation
- Implement proper error handling
- Use appropriate HTTP status codes
- Consider pagination for list endpoints
- Add proper documentation using docstrings

## Next Steps

- Learn about [Advanced CRUD Patterns](../advanced/crud_patterns.md)
- Understand Database Integration
- Explore Performance Optimization 
# Advanced CRUD Patterns in FastAPI

This guide covers advanced CRUD (Create, Read, Update, Delete) patterns and best practices in FastAPI applications.

## Table of Contents
- [Database Session Management](#database-session-management)
- [Basic CRUD Patterns](#basic-crud-patterns)
- [Extended Patterns](#extended-patterns)
- [Best Practices](#best-practices)
- [Common Patterns](#common-patterns)

## Database Session Management

### Async Session Factory
```python
from sqlalchemy.ext.asyncio import AsyncSession, create_async_engine, async_sessionmaker

# Create async engine
engine = create_async_engine("postgresql+asyncpg://user:pass@localhost/dbname")
AsyncSessionFactory = async_sessionmaker(engine, expire_on_commit=False)

# Session dependency
async def get_db() -> AsyncSession:
    async with AsyncSessionFactory() as session:
        yield session
        # Auto-rollback on exception; always closes session
```

## Basic CRUD Patterns

### Extracted Functions Pattern
```python
# crud.py
from sqlalchemy import select
from sqlalchemy.ext.asyncio import AsyncSession

async def create_item(db: AsyncSession, data: dict):
    obj = Model(**data)
    db.add(obj)
    await db.commit()
    await db.refresh(obj)
    return obj

async def get_item(db: AsyncSession, id: int):
    return await db.get(Model, id)

async def list_items(db: AsyncSession, skip: int = 0, limit: int = 100):
    result = await db.execute(select(Model).offset(skip).limit(limit))
    return result.scalars().all()

async def update_item(db: AsyncSession, id: int, data: dict):
    obj = await get_item(db, id)
    if not obj:
        return None
    for key, value in data.items():
        setattr(obj, key, value)
    await db.commit()
    await db.refresh(obj)
    return obj

async def delete_item(db: AsyncSession, id: int):
    obj = await get_item(db, id)
    if not obj:
        return None
    await db.delete(obj)
    await db.commit()
    return obj
```

### Inline Endpoints Pattern
```python
from fastapi import APIRouter, Depends, HTTPException
from sqlalchemy.ext.asyncio import AsyncSession

router = APIRouter()

@router.post("/items/")
async def create_item(
    item: ItemCreate,
    db: AsyncSession = Depends(get_db)
):
    obj = Model(**item.model_dump())
    db.add(obj)
    await db.commit()
    await db.refresh(obj)
    return obj

@router.get("/items/{id}")
async def read_item(
    id: int,
    db: AsyncSession = Depends(get_db)
):
    obj = await db.get(Model, id)
    if not obj:
        raise HTTPException(status_code=404, detail="Item not found")
    return obj

@router.put("/items/{id}")
async def update_item(
    id: int,
    item: ItemUpdate,
    db: AsyncSession = Depends(get_db)
):
    obj = await db.get(Model, id)
    if not obj:
        raise HTTPException(status_code=404, detail="Item not found")
    
    data = item.model_dump(exclude_unset=True)
    for key, value in data.items():
        setattr(obj, key, value)
    
    await db.commit()
    await db.refresh(obj)
    return obj

@router.delete("/items/{id}")
async def delete_item(
    id: int,
    db: AsyncSession = Depends(get_db)
):
    obj = await db.get(Model, id)
    if not obj:
        raise HTTPException(status_code=404, detail="Item not found")
    
    await db.delete(obj)
    await db.commit()
    return obj
```

## Extended Patterns

### Bulk Operations
```python
# Batch insert
async def bulk_create(db: AsyncSession, items: list[dict]):
    objects = [Model(**item) for item in items]
    db.add_all(objects)
    await db.commit()
    return objects

# Batch update
async def bulk_update(db: AsyncSession, ids: list[int], data: dict):
    stmt = update(Model).where(Model.id.in_(ids)).values(**data)
    await db.execute(stmt)
    await db.commit()
```

### Optimistic Locking
```python
from sqlalchemy import update

async def update_with_version(
    db: AsyncSession,
    id: int,
    data: dict,
    version: int
):
    stmt = (
        update(Model)
        .where(Model.id == id, Model.version == version)
        .values(**data, version=Model.version + 1)
    )
    result = await db.execute(stmt)
    if result.rowcount == 0:
        raise HTTPException(409, "Version conflict")
    await db.commit()
```

### Soft Deletes
```python
from datetime import datetime

async def soft_delete(db: AsyncSession, id: int):
    await db.execute(
        update(Model)
        .where(Model.id == id)
        .values(
            is_deleted=True,
            deleted_at=datetime.utcnow()
        )
    )
    await db.commit()

# Query with soft delete filter
async def list_active_items(db: AsyncSession):
    result = await db.execute(
        select(Model).where(Model.is_deleted == False)
    )
    return result.scalars().all()
```

### Multi-Resource Transactions
```python
async def create_with_related(
    db: AsyncSession,
    parent_data: dict,
    children_data: list[dict]
):
    async with db.begin():
        parent = Parent(**parent_data)
        parent.children = [Child(**child) for child in children_data]
        db.add(parent)
    return parent
```

## Best Practices

1. **Error Handling**
   - Use proper exception handling
   - Implement rollback mechanisms
   - Return meaningful error messages

2. **Validation**
   - Use Pydantic models for input validation
   - Implement business rule validation
   - Handle edge cases

3. **Performance**
   - Use appropriate indexes
   - Implement pagination
   - Optimize queries

4. **Security**
   - Implement proper authentication
   - Use role-based access control
   - Validate input data

## Common Patterns

### Pagination
```python
from typing import Optional
from fastapi import Query

async def list_items(
    db: AsyncSession,
    skip: int = Query(0, ge=0),
    limit: int = Query(100, ge=1, le=100)
):
    result = await db.execute(
        select(Model)
        .offset(skip)
        .limit(limit)
    )
    return result.scalars().all()
```

### Filtering
```python
from sqlalchemy import and_

async def filter_items(
    db: AsyncSession,
    status: Optional[str] = None,
    category: Optional[str] = None
):
    conditions = []
    if status:
        conditions.append(Model.status == status)
    if category:
        conditions.append(Model.category == category)
    
    query = select(Model)
    if conditions:
        query = query.where(and_(*conditions))
    
    result = await db.execute(query)
    return result.scalars().all()
```

### Sorting
```python
from sqlalchemy import desc

async def list_sorted_items(
    db: AsyncSession,
    sort_by: str = "created_at",
    order: str = "desc"
):
    column = getattr(Model, sort_by)
    if order == "desc":
        column = desc(column)
    
    result = await db.execute(
        select(Model).order_by(column)
    )
    return result.scalars().all()
```
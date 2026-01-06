# Advanced CRUD Patterns (FastAPI + SQLAlchemy)

Practical patterns you'll reuse in real apps.

## Async Session Dependency
```python
from sqlalchemy.ext.asyncio import AsyncSession, create_async_engine, async_sessionmaker

engine = create_async_engine(DB_URL, pool_pre_ping=True)
SessionFactory = async_sessionmaker(engine, expire_on_commit=False)

async def get_db() -> AsyncSession:
    async with SessionFactory() as session:
        yield session
```

## Service Layer (Keep Endpoints Thin)
```python
async def create_item(db: AsyncSession, data: dict):
    obj = Model(**data)
    db.add(obj)
    await db.commit()
    await db.refresh(obj)
    return obj
```

## Partial Updates
```python
data = payload.model_dump(exclude_unset=True)
for k, v in data.items():
    setattr(obj, k, v)
await db.commit()
```

## Soft Deletes
```python
await db.execute(
    update(Model).where(Model.id == id).values(is_deleted=True)
)
await db.commit()
```

## Optimistic Locking
```python
stmt = (
    update(Model)
    .where(Model.id == id, Model.version == version)
    .values(**data, version=Model.version + 1)
)
result = await db.execute(stmt)
if result.rowcount == 0:
    raise HTTPException(409, "version conflict")
await db.commit()
```

## Pagination
```python
q = select(Model).offset(skip).limit(limit)
rows = (await db.execute(q)).scalars().all()
```

## Filtering + Sorting
```python
q = select(Model)
if status:
    q = q.where(Model.status == status)
q = q.order_by(Model.created_at.desc())
```

## Notes
- Prefer transactions for multi-step writes: `async with db.begin(): ...`
- Avoid N+1 queries; use `selectinload` / `joinedload` where needed.

Next: [JWT](../security/jwt.md)

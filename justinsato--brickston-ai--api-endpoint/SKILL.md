---
name: api-endpoint
description: Create FastAPI endpoints with proper patterns for brickston-ai Use when this capability is needed.
metadata:
  author: justinsato
---

# API Endpoint Development Skill

## Overview
This skill guides you through creating FastAPI endpoints following brickston-ai conventions.

## Directory Structure
```
apps/api/app/
├── routes/           # API route handlers
│   ├── discovery/    # Scrapers, listings
│   ├── insights/     # Analytics endpoints
│   └── ...
├── domain/           # Business logic/services
│   ├── properties/
│   ├── residents/
│   └── ...
├── core/             # Shared utilities
│   ├── database.py
│   └── config.py
└── models/           # Pydantic models
```

## Creating a New Endpoint

### Step 1: Create Route File
Location: `apps/api/app/routes/<domain>/<endpoint_name>.py`

```python
from fastapi import APIRouter, Depends, HTTPException
from sqlalchemy.ext.asyncio import AsyncSession
from app.core.database import get_db

router = APIRouter(prefix="/<resource>", tags=["<domain>"])

@router.get("/")
async def get_resources(db: AsyncSession = Depends(get_db)):
    """Get all resources."""
    try:
        result = await db.execute(text("SELECT * FROM table_name"))
        return {"data": result.mappings().all()}
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

@router.get("/{id}")
async def get_resource(id: int, db: AsyncSession = Depends(get_db)):
    """Get single resource by ID."""
    # Implementation
    pass

@router.post("/")
async def create_resource(data: ResourceCreate, db: AsyncSession = Depends(get_db)):
    """Create a new resource."""
    # Implementation
    pass
```

### Step 2: Register Router
Add to `apps/api/app/main.py`:
```python
from app.routes.domain.endpoint_name import router as endpoint_router
app.include_router(endpoint_router, prefix="/api/v1")
```

### Step 3: Create Service Layer (Optional)
For complex business logic, create a service:
`apps/api/app/domain/<domain>/service.py`

```python
from sqlalchemy.ext.asyncio import AsyncSession
from sqlalchemy import text

class ResourceService:
    def __init__(self, db: AsyncSession):
        self.db = db

    async def get_all(self):
        result = await self.db.execute(text("SELECT * FROM resources"))
        return result.mappings().all()
```

## Response Patterns

### Standard List Response
```python
@router.get("/")
async def list_items(db: AsyncSession = Depends(get_db)):
    return {
        "data": items,
        "count": len(items)
    }
```

### Paginated Response
```python
@router.get("/")
async def list_items(
    page: int = 1,
    per_page: int = 20,
    db: AsyncSession = Depends(get_db)
):
    offset = (page - 1) * per_page
    return {
        "data": items,
        "page": page,
        "per_page": per_page,
        "total": total_count
    }
```

## Error Handling Pattern
```python
from contextlib import asynccontextmanager

@asynccontextmanager
async def handle_db_errors():
    try:
        yield
    except Exception as e:
        await db.rollback()
        raise HTTPException(status_code=500, detail=str(e))
```

## Testing
```bash
cd apps/api
pytest tests/routes/test_<endpoint>.py -v
```

## Checklist
- [ ] Router created in `routes/<domain>/`
- [ ] Router registered in `main.py`
- [ ] Proper error handling with try/except
- [ ] Database sessions properly managed
- [ ] Pydantic models for request/response validation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/justinsato) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

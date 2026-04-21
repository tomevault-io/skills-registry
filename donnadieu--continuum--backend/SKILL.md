---
name: backend
description: This skill provides comprehensive guidance for Python and FastAPI development in Use when this capability is needed.
metadata:
  author: donnadieu
---

# Backend Development for Continuum

This skill provides comprehensive guidance for Python and FastAPI development in
the Continuum platform.

## When to Use This Skill

Invoke this skill for any backend development task:

- Creating new API endpoints
- Implementing service layer logic
- Working with SQLAlchemy models
- Writing Pydantic schemas for request/response
- Implementing background workers
- Working with Neo4j knowledge graph
- Writing or updating tests with pytest

## Core Principles

1. **Async everywhere** - All I/O operations use async/await
2. **Pydantic for validation** - Request/response schemas with strict typing
3. **Repository pattern** - Data access through repository classes
4. **Service layer** - Business logic in services, not routes
5. **Provenance-first** - Every fact must have evidence
6. **User isolation** - RLS policies enforce data boundaries
7. **Type hints** - Full typing with mypy strict mode

## Continuum Project Structure

```
backend/
├── api/v1/                    # API routes
│   ├── routes/               # Route modules
│   │   ├── auth.py
│   │   ├── chat.py
│   │   ├── entities.py
│   │   └── integrations.py
│   └── schemas/              # Pydantic request/response models
│       ├── auth.py
│       ├── chat.py
│       └── entities.py
├── core/                      # Core business logic
│   ├── provenance.py         # Evidence tracking
│   └── confidence.py         # Confidence scoring
├── db/
│   ├── models/               # SQLAlchemy models
│   │   ├── user.py
│   │   ├── data_source.py
│   │   └── conversation.py
│   ├── repositories/         # Data access layer
│   │   ├── base.py
│   │   └── user_repository.py
│   └── session.py            # Database session management
├── services/                  # Business logic services
│   ├── sync/                 # Data synchronization
│   ├── confidence.py         # Confidence scoring service
│   └── prompt_queue.py       # Progressive capture
├── memory/                    # Cognitive memory layer
│   ├── schemas/              # JSON-LD canonical records
│   │   ├── base.py
│   │   ├── person.py
│   │   └── evidence.py
│   └── service.py            # MemoryService interface
├── graph/                     # Neo4j integration
│   ├── service.py            # Neo4jService
│   └── queries.py            # Cypher query helpers
├── storage/                   # Storage abstraction
│   ├── interface.py          # StorageService interface
│   └── supabase.py           # Supabase implementation
├── extractors/               # LLM-powered entity extraction
│   ├── person.py
│   ├── event.py
│   └── relationship.py
├── query/                     # RAG retrieval system
│   ├── retriever.py          # RetrievalOrchestrator
│   ├── analysis.py           # QueryAnalyzer
│   └── response.py           # ResponseGenerator
├── workers/                   # Background task workers
│   ├── base.py               # Task infrastructure
│   ├── scheduler.py          # Task scheduling
│   └── tasks/                # Task implementations
├── integrations/             # External API integrations
│   └── google/               # Google OAuth, Gmail, Calendar
├── middleware/               # Request middleware
│   └── auth.py               # JWT verification
├── config/
│   └── settings.py           # Pydantic Settings
└── main.py                   # FastAPI app entry point
```

## Workflow: Choose Your Task

Before proceeding, identify the type of work needed and load the appropriate
reference documentation.

### For API Endpoint Work

**When:** Creating routes, handling requests, returning responses

**Read:** `references/api-routes.md`

Common patterns:

- Use `APIRouter` with prefix and tags
- Dependency injection for auth and services
- Pydantic schemas for request/response validation
- Proper HTTP status codes and error responses

### For Database Work

**When:** Working with models, queries, migrations

**Read:** `references/database.md`

Common patterns:

- SQLAlchemy 2.0 async patterns
- Repository classes for data access
- Proper session management with context managers
- Index and relationship optimization

### For Service Layer Work

**When:** Implementing business logic, orchestrating operations

**Read:** `references/services.md`

Common patterns:

- Services receive dependencies via constructor
- Async methods for all operations
- Error handling with custom exceptions
- Transaction management

### For Memory/Graph Work

**When:** Working with canonical records, Neo4j, semantic search

**Read:** `references/memory-graph.md`

Common patterns:

- MemoryService as unified interface
- Storage as source of truth, Neo4j for indexing
- JSON-LD canonical record schemas
- Vector similarity search

### For Testing Work

**When:** Writing tests, mocking dependencies, fixtures

**Read:** `references/testing.md`

Common patterns:

- pytest + pytest-asyncio
- Fixtures for database sessions and services
- Mock external APIs (OpenAI, Google)
- Transaction rollback for test isolation

## Quick Decision Tree

```
Is this about...
├─ API routes/endpoints? → Read api-routes.md
├─ Database models/queries? → Read database.md
├─ Business logic/services? → Read services.md
├─ Memory records/Neo4j? → Read memory-graph.md
├─ Background jobs? → Read workers.md
├─ External integrations? → Read integrations.md
└─ Tests? → Read testing.md
```

## Common Continuum Patterns

### FastAPI Route with Dependencies

```python
from fastapi import APIRouter, Depends, HTTPException, status
from backend.api.v1.schemas.entities import EntityResponse, EntityCreate
from backend.middleware.auth import get_current_user
from backend.services.entity_service import EntityService

router = APIRouter(prefix="/entities", tags=["entities"])

@router.get("/{entity_id}", response_model=EntityResponse)
async def get_entity(
    entity_id: UUID,
    user: User = Depends(get_current_user),
    service: EntityService = Depends(get_entity_service),
) -> EntityResponse:
    entity = await service.get_entity(entity_id, user.id)
    if not entity:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail="Entity not found"
        )
    return EntityResponse.model_validate(entity)
```

### Pydantic Schema

```python
from pydantic import BaseModel, Field
from datetime import datetime
from uuid import UUID

class EntityBase(BaseModel):
    name: str = Field(..., min_length=1, max_length=255)
    entity_type: str
    confidence: float = Field(ge=0.0, le=1.0)

class EntityCreate(EntityBase):
    pass

class EntityResponse(EntityBase):
    id: UUID
    user_id: UUID
    created_at: datetime
    updated_at: datetime

    model_config = {"from_attributes": True}
```

### SQLAlchemy Model

```python
from sqlalchemy import Column, String, Float, ForeignKey, DateTime
from sqlalchemy.dialects.postgresql import UUID
from sqlalchemy.orm import relationship
from backend.db.base import Base
import uuid

class Entity(Base):
    __tablename__ = "entities"

    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    user_id = Column(UUID(as_uuid=True), ForeignKey("users.id"), nullable=False)
    name = Column(String(255), nullable=False)
    entity_type = Column(String(50), nullable=False)
    confidence = Column(Float, default=0.0)
    created_at = Column(DateTime, server_default=func.now())
    updated_at = Column(DateTime, server_default=func.now(), onupdate=func.now())

    # Relationships
    user = relationship("User", back_populates="entities")
    evidence = relationship("Evidence", back_populates="entity")

    # Indexes
    __table_args__ = (
        Index("ix_entities_user_id", "user_id"),
        Index("ix_entities_name", "name"),
    )
```

### Repository Pattern

```python
from sqlalchemy.ext.asyncio import AsyncSession
from sqlalchemy import select
from backend.db.models.entity import Entity

class EntityRepository:
    def __init__(self, session: AsyncSession):
        self.session = session

    async def get_by_id(self, entity_id: UUID) -> Entity | None:
        result = await self.session.execute(
            select(Entity).where(Entity.id == entity_id)
        )
        return result.scalar_one_or_none()

    async def get_by_user(
        self, user_id: UUID, limit: int = 100, offset: int = 0
    ) -> list[Entity]:
        result = await self.session.execute(
            select(Entity)
            .where(Entity.user_id == user_id)
            .order_by(Entity.created_at.desc())
            .limit(limit)
            .offset(offset)
        )
        return list(result.scalars().all())

    async def create(self, entity: Entity) -> Entity:
        self.session.add(entity)
        await self.session.commit()
        await self.session.refresh(entity)
        return entity
```

### Service Layer

```python
from backend.db.repositories.entity_repository import EntityRepository
from backend.memory.service import MemoryService

class EntityService:
    def __init__(
        self,
        repository: EntityRepository,
        memory_service: MemoryService,
    ):
        self.repository = repository
        self.memory_service = memory_service

    async def get_entity(self, entity_id: UUID, user_id: UUID) -> Entity | None:
        entity = await self.repository.get_by_id(entity_id)
        if entity and entity.user_id != user_id:
            return None  # User isolation
        return entity

    async def create_entity(
        self, user_id: UUID, data: EntityCreate
    ) -> Entity:
        entity = Entity(
            user_id=user_id,
            name=data.name,
            entity_type=data.entity_type,
            confidence=data.confidence,
        )
        entity = await self.repository.create(entity)

        # Index in Neo4j
        await self.memory_service.index_entity(entity)

        return entity
```

### Async Database Session

```python
from contextlib import asynccontextmanager
from sqlalchemy.ext.asyncio import AsyncSession, create_async_engine
from sqlalchemy.orm import sessionmaker

engine = create_async_engine(settings.DATABASE_URL, echo=settings.DEBUG)
async_session_maker = sessionmaker(engine, class_=AsyncSession, expire_on_commit=False)

@asynccontextmanager
async def get_session() -> AsyncSession:
    async with async_session_maker() as session:
        try:
            yield session
        except Exception:
            await session.rollback()
            raise
        finally:
            await session.close()

# Usage in routes
async def get_db_session() -> AsyncSession:
    async with get_session() as session:
        yield session
```

### MemoryService Pattern

```python
from backend.storage.interface import StorageService
from backend.graph.service import Neo4jService
from backend.memory.schemas.person import PersonRecord

class MemoryService:
    def __init__(self, storage: StorageService, graph: Neo4jService | None):
        self.storage = storage
        self.graph = graph

    async def save_person(self, user_id: UUID, person: PersonRecord) -> None:
        # Storage is source of truth
        await self.storage.write_memory(user_id, "person", person.id, person)

        # Index in Neo4j if available
        if self.graph:
            await self.graph.index_person(person)

    async def get_person(self, user_id: UUID, person_id: str) -> PersonRecord | None:
        return await self.storage.read_memory(user_id, "person", person_id, PersonRecord)

    async def semantic_search(
        self, user_id: UUID, embedding: list[float], limit: int = 10
    ) -> list[PersonRecord]:
        if not self.graph:
            return []
        return await self.graph.vector_search(user_id, embedding, limit)
```

### Background Worker Task

```python
from backend.workers.base import task_registry

@task_registry.register("sync.process_extraction")
async def process_extraction(user_id: str, source_type: str) -> dict:
    """Extract entities from raw synced data."""
    async with get_session() as session:
        memory_service = await get_memory_service()
        extractor = PersonExtractor(memory_service)

        # Process raw data
        raw_items = await memory_service.list_raw(user_id, source_type)
        for item in raw_items:
            await extractor.extract_from_raw(user_id, item)

        return {"processed": len(raw_items)}
```

### Test with Fixtures

```python
import pytest
from httpx import AsyncClient
from backend.main import app
from backend.db.session import get_session

@pytest.fixture
async def client():
    async with AsyncClient(app=app, base_url="http://test") as client:
        yield client

@pytest.fixture
async def db_session():
    async with get_session() as session:
        yield session
        await session.rollback()

@pytest.fixture
async def test_user(db_session):
    user = User(email="test@example.com")
    db_session.add(user)
    await db_session.commit()
    return user

@pytest.mark.asyncio
async def test_get_entity(client, test_user, db_session):
    # Create test entity
    entity = Entity(user_id=test_user.id, name="Test", entity_type="person")
    db_session.add(entity)
    await db_session.commit()

    # Test endpoint
    response = await client.get(
        f"/api/v1/entities/{entity.id}",
        headers={"Authorization": f"Bearer {test_user.token}"}
    )
    assert response.status_code == 200
    assert response.json()["name"] == "Test"
```

## Multi-File Changes

When making changes that span multiple files:

1. **Read all affected files completely** before making changes
2. **Identify all references** using global search
3. **Update in dependency order**:
   - Schemas first (`api/v1/schemas/`, `memory/schemas/`)
   - Models next (`db/models/`)
   - Repositories (`db/repositories/`)
   - Services (`services/`)
   - Routes last (`api/v1/routes/`)
4. **Run tests incrementally** after each major change

## Before You Start

1. Identify the task type using the decision tree above
2. Read the appropriate reference file(s) for your task
3. Review existing similar code for consistency
4. Check `api/v1/schemas/` for existing Pydantic models
5. Verify no duplicate functionality exists

## After Implementation

1. Ensure all functions have type hints
2. Verify async/await is used correctly for all I/O
3. Check error handling covers failure modes
4. Confirm user isolation (user_id checks)
5. Run tests: `uv run pytest`
6. Run type checking: `uv run mypy backend`
7. Run linting: `uv run ruff check backend`

## Getting Help

If uncertain about:

- **Route structure** → Look at `api/v1/routes/`
- **Schema patterns** → Check `api/v1/schemas/`
- **Database patterns** → Review `db/models/` and `db/repositories/`
- **Service patterns** → Browse `services/`
- **Memory patterns** → Check `memory/service.py`

## Reference Files

All reference files are in `references/` directory:

- `api-routes.md` - FastAPI routes, dependencies, error handling
- `database.md` - SQLAlchemy models, repositories, migrations
- `services.md` - Service layer patterns, dependency injection
- `memory-graph.md` - MemoryService, Neo4j, canonical records
- `workers.md` - Background tasks, scheduling
- `integrations.md` - External API patterns (Google, OpenAI)
- `testing.md` - pytest patterns, fixtures, mocking

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/donnadieu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

---
name: sqlmodel
description: | Use when this capability is needed.
metadata:
  author: jamaliumair
---

# SQLModel ORM Skill

SQLModel combines SQLAlchemy (database ORM) with Pydantic (data validation) into a single library with full type hint support.

## Quick Reference

| Feature | Reference File |
|---------|----------------|
| Database setup, async engine | [references/database-setup.md](references/database-setup.md) |
| Model definitions, validations | [references/models.md](references/models.md) |
| Relationships (1:1, 1:N, M:N) | [references/relationships.md](references/relationships.md) |
| Queries, filters, joins | [references/queries.md](references/queries.md) |

## Core Concepts

### SQLModel = SQLAlchemy + Pydantic

```python
from sqlmodel import SQLModel, Field

# Schema only (no table=True)
class UserBase(SQLModel):
    email: str
    name: str

# Database table (table=True)
class User(UserBase, table=True):
    id: int | None = Field(default=None, primary_key=True)
```

### Minimal Setup

```python
from sqlmodel import SQLModel, Field, create_engine, Session

engine = create_engine("sqlite:///database.db")
SQLModel.metadata.create_all(engine)

with Session(engine) as session:
    user = User(email="test@example.com", name="Test")
    session.add(user)
    session.commit()
```

### Async Setup (Recommended)

```python
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession, async_sessionmaker
from sqlmodel import SQLModel

engine = create_async_engine("postgresql+asyncpg://user:pass@localhost/db")
async_session = async_sessionmaker(engine, class_=AsyncSession, expire_on_commit=False)

async def get_session():
    async with async_session() as session:
        yield session
```

## Dependencies

```toml
[project]
dependencies = [
    "sqlmodel>=0.0.22",
    "asyncpg>=0.30.0",       # PostgreSQL async
    "greenlet>=3.1.0",       # Async support
    "alembic>=1.14.0",       # Migrations
]
```

## Common Patterns

### Model with Validation

```python
from pydantic import field_validator
from sqlmodel import SQLModel, Field

class User(SQLModel, table=True):
    id: int | None = Field(default=None, primary_key=True)
    email: str = Field(unique=True, index=True)
    age: int = Field(ge=0, le=150)

    @field_validator("email")
    @classmethod
    def validate_email(cls, v: str) -> str:
        if "@" not in v:
            raise ValueError("Invalid email")
        return v.lower()
```

### CRUD Operations

```python
from sqlalchemy import select

# Create
user = User(email="test@example.com", name="Test")
session.add(user)
await session.commit()

# Read
stmt = select(User).where(User.email == "test@example.com")
result = await session.execute(stmt)
user = result.scalar_one_or_none()

# Update
user.name = "Updated"
await session.commit()

# Delete
await session.delete(user)
await session.commit()
```

### Relationships Quick Reference

```python
# One-to-Many
class Team(SQLModel, table=True):
    members: list["User"] = Relationship(back_populates="team")

class User(SQLModel, table=True):
    team_id: int | None = Field(foreign_key="team.id")
    team: Team | None = Relationship(back_populates="members")

# Many-to-Many (requires link table)
class UserRoleLink(SQLModel, table=True):
    user_id: int = Field(foreign_key="user.id", primary_key=True)
    role_id: int = Field(foreign_key="role.id", primary_key=True)
```

## Migration Commands

```bash
alembic init alembic                          # Initialize
alembic revision --autogenerate -m "message"  # Create migration
alembic upgrade head                          # Apply migrations
alembic downgrade -1                          # Rollback one step
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jamaliumair) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

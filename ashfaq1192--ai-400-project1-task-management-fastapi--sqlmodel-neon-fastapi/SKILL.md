---
name: sqlmodel-neon-fastapi
description: | Use when this capability is needed.
metadata:
  author: ashfaq1192
---

# SQLModel + NEON FastAPI Builder

Build FastAPI applications with SQLModel ORM connected to NEON serverless PostgreSQL.

## Before Implementation

Gather context before building:

| Source | Gather |
|--------|--------|
| **Codebase** | Existing models, project structure, naming conventions |
| **Conversation** | Entity names, fields, relationship types needed |
| **Skill References** | Patterns from `references/` for relationships, CRUD, pagination |
| **User Guidelines** | Project-specific conventions |

## Quick Start

### 1. Install Dependencies
```bash
pip install sqlmodel psycopg2-binary python-dotenv
```

### 2. Environment Setup
```env
# .env
DATABASE_URL=postgresql://user:pass@ep-xxx.region.aws.neon.tech/dbname?sslmode=require
```

### 3. Database Connection
```python
# database.py
from sqlmodel import SQLModel, Session, create_engine
from dotenv import load_dotenv
import os

load_dotenv()

engine = create_engine(
    os.getenv("DATABASE_URL"),
    pool_pre_ping=True,
    pool_recycle=300,
)

def get_session():
    with Session(engine) as session:
        yield session

def create_db_and_tables():
    SQLModel.metadata.create_all(engine)
```

### 4. Basic Model
```python
# models.py
from sqlmodel import Field, SQLModel

class ItemBase(SQLModel):
    name: str = Field(index=True)
    price: float

class Item(ItemBase, table=True):
    id: int | None = Field(default=None, primary_key=True)

class ItemCreate(ItemBase):
    pass

class ItemPublic(ItemBase):
    id: int
```

### 5. CRUD Endpoint
```python
# main.py
from fastapi import FastAPI, Depends, HTTPException
from sqlmodel import Session, select

app = FastAPI()

@app.post("/items/", response_model=ItemPublic, status_code=201)
def create_item(item: ItemCreate, session: Session = Depends(get_session)):
    db_item = Item.model_validate(item)
    session.add(db_item)
    session.commit()
    session.refresh(db_item)
    return db_item
```

## Model Patterns

### Separate Models Pattern (Recommended)

Use inheritance to avoid field duplication:

```python
class HeroBase(SQLModel):
    name: str = Field(index=True)
    secret_name: str
    age: int | None = None

class Hero(HeroBase, table=True):
    id: int | None = Field(default=None, primary_key=True)

class HeroCreate(HeroBase):
    pass

class HeroPublic(HeroBase):
    id: int

class HeroUpdate(SQLModel):
    name: str | None = None
    secret_name: str | None = None
    age: int | None = None
```

### One-to-Many Relationship

```python
from sqlmodel import Field, Relationship, SQLModel

class Team(SQLModel, table=True):
    id: int | None = Field(default=None, primary_key=True)
    name: str = Field(index=True)

    heroes: list["Hero"] = Relationship(back_populates="team")

class Hero(SQLModel, table=True):
    id: int | None = Field(default=None, primary_key=True)
    name: str = Field(index=True)

    team_id: int | None = Field(default=None, foreign_key="team.id")
    team: Team | None = Relationship(back_populates="heroes")
```

### Many-to-Many Relationship

```python
class HeroTeamLink(SQLModel, table=True):
    hero_id: int = Field(foreign_key="hero.id", primary_key=True)
    team_id: int = Field(foreign_key="team.id", primary_key=True)

class Hero(SQLModel, table=True):
    id: int | None = Field(default=None, primary_key=True)
    name: str

    teams: list["Team"] = Relationship(
        back_populates="heroes",
        link_model=HeroTeamLink
    )

class Team(SQLModel, table=True):
    id: int | None = Field(default=None, primary_key=True)
    name: str

    heroes: list["Hero"] = Relationship(
        back_populates="teams",
        link_model=HeroTeamLink
    )
```

## CRUD Endpoints

### Full CRUD Pattern

```python
from fastapi import Query

# CREATE
@app.post("/heroes/", response_model=HeroPublic, status_code=201)
def create_hero(hero: HeroCreate, session: Session = Depends(get_session)):
    db_hero = Hero.model_validate(hero)
    session.add(db_hero)
    session.commit()
    session.refresh(db_hero)
    return db_hero

# READ (list with pagination)
@app.get("/heroes/", response_model=list[HeroPublic])
def read_heroes(
    session: Session = Depends(get_session),
    offset: int = 0,
    limit: int = Query(default=100, le=100)
):
    heroes = session.exec(select(Hero).offset(offset).limit(limit)).all()
    return heroes

# READ (single)
@app.get("/heroes/{hero_id}", response_model=HeroPublic)
def read_hero(hero_id: int, session: Session = Depends(get_session)):
    hero = session.get(Hero, hero_id)
    if not hero:
        raise HTTPException(status_code=404, detail="Hero not found")
    return hero

# UPDATE
@app.patch("/heroes/{hero_id}", response_model=HeroPublic)
def update_hero(
    hero_id: int,
    hero: HeroUpdate,
    session: Session = Depends(get_session)
):
    db_hero = session.get(Hero, hero_id)
    if not db_hero:
        raise HTTPException(status_code=404, detail="Hero not found")
    hero_data = hero.model_dump(exclude_unset=True)
    db_hero.sqlmodel_update(hero_data)
    session.add(db_hero)
    session.commit()
    session.refresh(db_hero)
    return db_hero

# DELETE
@app.delete("/heroes/{hero_id}", status_code=204)
def delete_hero(hero_id: int, session: Session = Depends(get_session)):
    hero = session.get(Hero, hero_id)
    if not hero:
        raise HTTPException(status_code=404, detail="Hero not found")
    session.delete(hero)
    session.commit()
```

## NEON Configuration

### Connection String Format
```
postgresql://[user]:[password]@[endpoint].neon.tech/[database]?sslmode=require
```

### Engine Settings for NEON
```python
engine = create_engine(
    DATABASE_URL,
    pool_pre_ping=True,      # Verify connections (handles scale-to-zero)
    pool_recycle=300,        # Recycle connections every 5 min
    pool_size=5,             # Connection pool size
    max_overflow=10,         # Extra connections allowed
)
```

### Connection Pooling (High Traffic)

Add `-pooler` to endpoint for PgBouncer pooling:
```
postgresql://user:pass@ep-xxx-pooler.region.aws.neon.tech/db?sslmode=require
```

## Resources

### References
- [api_reference.md](references/api_reference.md) - SQLModel & NEON API reference
- [relationships.md](references/relationships.md) - Detailed relationship patterns (1:1, 1:N, N:N, self-referential)
- [crud_patterns.md](references/crud_patterns.md) - Advanced CRUD with filtering, pagination, sorting

### Assets
- [conftest.py](assets/conftest.py) - Pytest fixtures for testing with SQLModel
- [models_template.py](assets/models_template.py) - Starter model templates with mixins

## Checklist

Before completing implementation:

- [ ] `.env` file with `DATABASE_URL` (gitignored)
- [ ] Base/Create/Public/Update model classes defined
- [ ] Foreign keys use `foreign_key="table.column"` format
- [ ] Relationships use `back_populates` on both sides
- [ ] Session dependency uses `yield` pattern
- [ ] Engine has `pool_pre_ping=True` for NEON
- [ ] Pagination on list endpoints with `le=100` limit
- [ ] HTTPException 404 for missing resources
- [ ] Status codes: 201 (create), 204 (delete), 200 (others)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ashfaq1192) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

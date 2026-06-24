---
name: fastapi-db-migrate
description: > Use when this capability is needed.
metadata:
  author: abhayla
---

# Database Migration Helper (FastAPI + Alembic)

Automates Alembic migration creation and model import location updates.

**Scope:** New model creation with migration generation. For modifying existing models, manually edit the model file and run `alembic revision --autogenerate`. For stack-neutral migration support (Prisma, Knex, Django, etc.), use `/db-migrate`. After any migration, verify with `/db-migrate-verify`.

**Note:** The `backend/` path is a convention. If your project uses a different layout, adjust paths accordingly.

**Request:** $ARGUMENTS

---

## Modes

| Mode | Trigger | Behavior |
|------|---------|----------|
| **new-model** | Model name provided | Create model file + update import locations + generate migration |
| **migrate** | "run" or "migrate" | Run `alembic upgrade head` |
| **status** | "status" | Show current migration status |
| **check** | "check" | Verify all import locations are in sync |

---

## STEP 1: New Model Mode

### 1. Create the Model File

Create `backend/app/models/{model_name_snake}.py` following existing patterns:

```python
from sqlalchemy import Column, String, DateTime, ForeignKey, func
from sqlalchemy.dialects.postgresql import UUID
from app.db.base import Base
import uuid

class ModelName(Base):
    __tablename__ = "table_name"
    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    created_at = Column(DateTime(timezone=True), server_default=func.now())
    updated_at = Column(DateTime(timezone=True), server_default=func.now(), onupdate=func.now())
```

### 2. Update Import Locations

Find all locations where models are imported and add the new one:

```bash
cd backend && grep -rn "from app.models" app/db/ app/models/__init__.py tests/conftest.py
```

Update each location to include the new model import.

### 3. Generate Migration

```bash
cd backend && alembic revision --autogenerate -m "add {model_name} table"
```

### 4. Run Migration

```bash
cd backend && alembic upgrade head
```

### 5. Verify

```bash
cd backend && PYTHONPATH=. python -c "from app.models import *; print('All imports OK')"
```

---

## STEP 2: Check Mode

Verify all model imports are in sync across required locations:

1. List all model files:
   ```bash
   ls backend/app/models/*.py | grep -v __init__ | grep -v __pycache__
   ```
2. For each model file, check it's imported in:
   - `backend/app/models/__init__.py`
   - `backend/app/db/base.py` (or equivalent base import file)
   - `backend/tests/conftest.py` (if test fixtures reference models)
3. Report mismatches:
   ```
   Import Sync Check:
   - model_name.py: ✓ __init__.py | ✓ base.py | ✗ conftest.py (MISSING)
   ```

## STEP 3: Status Mode

```bash
cd backend && alembic current && alembic history --verbose | head -20
```

Report current head, pending migrations, and any branch divergence.

---

## Output Summary

After any mode, report:
```
Migration Summary:
  Mode: <new-model|migrate|status|check>
  Model: <name or N/A>
  Files changed: <list>
  Migration: <generated|applied|checked|N/A>
  Import sync: <all OK|N mismatches>
```

---

## CRITICAL RULES

- MUST NOT skip any import location — missing imports cause silent failures at runtime
- MUST run sync check (Step 2) after adding a model — Why: partial imports break Alembic autogenerate
- MUST follow existing naming conventions (snake_case files, PascalCase classes) — Why: inconsistent naming causes import confusion
- MUST NOT modify existing migrations — generate new ones instead
- After migration, verify with `/db-migrate-verify` if available

---
> Source: [abhayla/claude-best-practices](https://github.com/abhayla/claude-best-practices) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->

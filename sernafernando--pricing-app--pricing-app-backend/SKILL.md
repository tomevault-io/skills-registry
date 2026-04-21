---
name: pricing-app-backend
description: FastAPI backend patterns for Pricing App - SQLAlchemy, Alembic, auth, permissions Use when this capability is needed.
metadata:
  author: sernafernando
---

# Pricing App Backend - FastAPI + SQLAlchemy

---

## CRITICAL RULES - NON-NEGOTIABLE

### Type Hints
- ALWAYS: Full type hints on all functions: `def get_user(user_id: int) -> User:`
- NEVER: Missing return types or parameter types

### Exception Handling
- ALWAYS: Specific exceptions: `except ValueError:`, `except HTTPException:`
- NEVER: Bare `except:` blocks

### FastAPI Endpoints
- ALWAYS: Explicit response models: `@router.get("/users", response_model=List[UserResponse])`
- ALWAYS: Use dependency injection: `current_user: User = Depends(get_current_user)`
- ALWAYS: Proper HTTP status codes (200, 201, 400, 401, 403, 404, 422, 500)
- ALWAYS: Docstrings explaining business logic

### Pydantic v2 Syntax (CRITICAL)
- ALWAYS: Use `model_config = ConfigDict(...)` instead of `class Config:`
- ALWAYS: Use `SettingsConfigDict` for BaseSettings classes
- ALWAYS: Use `.model_dump()` instead of `.dict()`
- ALWAYS: Use `.model_dump_json()` instead of `.json()`
- ALWAYS: Import `ConfigDict` from `pydantic` when needed
- NEVER: Mix v1 and v2 syntax in the same codebase
- NEVER: Use deprecated `.dict()`, `.json()`, or `class Config:` patterns

### Database Models
- ALWAYS: Explicit column types: `Column(String(255))` not `Column(String)`
- ALWAYS: Create Alembic migrations for schema changes
- ALWAYS: Name migrations descriptively: `YYYYMMDD_description.py`
- ALWAYS: Use relationships: `relationship("User", back_populates="items")`
- ALWAYS: Add indexes for foreign keys and frequently queried columns

### Security
- ALWAYS: Hash passwords with bcrypt
- ALWAYS: Validate JWT tokens with `get_current_user` dependency
- ALWAYS: Check permissions before write operations: `tienePermiso(user, "write")`
- NEVER: Store passwords in plain text
- NEVER: Log sensitive data (passwords, tokens)

---

## PROJECT STRUCTURE

```
backend/
├── app/
│   ├── main.py              # FastAPI app initialization
│   ├── api/                 # (legacy - being migrated)
│   ├── routers/             # Route handlers (NEW pattern)
│   │   ├── auth.py
│   │   ├── productos.py
│   │   ├── ventas.py
│   │   └── ...
│   ├── models/              # SQLAlchemy models
│   │   ├── user.py
│   │   ├── producto.py
│   │   └── ...
│   ├── services/            # Business logic
│   │   ├── pricing_service.py
│   │   ├── ml_service.py
│   │   └── ...
│   ├── core/                # Config, security, database
│   │   ├── config.py
│   │   ├── database.py
│   │   ├── security.py
│   │   └── deps.py
│   ├── utils/               # Helper functions
│   └── scripts/             # Cron jobs, data sync
├── alembic/
│   ├── versions/            # DB migrations
│   └── env.py
└── requirements.txt
```

---

## PATTERNS

### Pydantic v2 Models (Response Schemas)

**✅ CORRECT (Pydantic v2):**
```python
from pydantic import BaseModel, ConfigDict, Field

class ProductoResponse(BaseModel):
    id: int
    codigo: str
    descripcion: str
    precio: float = Field(gt=0, description="Precio debe ser mayor a 0")
    
    # Pydantic v2 syntax
    model_config = ConfigDict(from_attributes=True)

class ProductoCreate(BaseModel):
    codigo: str = Field(min_length=1, max_length=50)
    descripcion: str
    precio: float = Field(gt=0)
    
    model_config = ConfigDict(
        json_schema_extra={
            "example": {
                "codigo": "PROD001",
                "descripcion": "Notebook Lenovo",
                "precio": 150000.0
            }
        }
    )
```

**❌ WRONG (Pydantic v1 - DEPRECATED):**
```python
# DO NOT USE THIS SYNTAX
class ProductoResponse(BaseModel):
    id: int
    codigo: str
    
    class Config:  # ❌ DEPRECATED
        orm_mode = True  # ❌ DEPRECATED

# DO NOT USE THIS
producto_dict = producto.dict()  # ❌ Use .model_dump() instead
producto_json = producto.json()  # ❌ Use .model_dump_json() instead
```

### Pydantic Settings (Configuration)

**✅ CORRECT (Pydantic v2):**
```python
from pydantic_settings import BaseSettings, SettingsConfigDict
from typing import Optional

class Settings(BaseSettings):
    DATABASE_URL: str
    SECRET_KEY: str
    ALGORITHM: str = "HS256"
    ACCESS_TOKEN_EXPIRE_MINUTES: int = 10080
    
    # Optional configs
    ML_CLIENT_ID: Optional[str] = None
    ML_CLIENT_SECRET: Optional[str] = None
    
    # Pydantic v2 settings syntax
    model_config = SettingsConfigDict(
        env_file=".env",
        case_sensitive=True,
        extra="ignore"
    )

settings = Settings()
```

**❌ WRONG (Pydantic v1 - DEPRECATED):**
```python
# DO NOT USE THIS SYNTAX
class Settings(BaseSettings):
    DATABASE_URL: str
    
    class Config:  # ❌ DEPRECATED
        env_file = ".env"
        case_sensitive = True
```

### Using Pydantic Models

**✅ CORRECT (Pydantic v2):**
```python
from pydantic import BaseModel

class ProductoCreate(BaseModel):
    codigo: str
    precio: float

# Convert to dict
producto = ProductoCreate(codigo="PROD001", precio=150000.0)
producto_dict = producto.model_dump()  # ✅ Correct

# Convert to JSON string
producto_json = producto.model_dump_json()  # ✅ Correct

# Exclude fields
producto_dict = producto.model_dump(exclude={"precio"})  # ✅ Correct

# Include only specific fields
producto_dict = producto.model_dump(include={"codigo"})  # ✅ Correct
```

**❌ WRONG (Pydantic v1 - DEPRECATED):**
```python
# DO NOT USE THESE
producto_dict = producto.dict()  # ❌ DEPRECATED - Use .model_dump()
producto_json = producto.json()  # ❌ DEPRECATED - Use .model_dump_json()
producto_dict = producto.dict(exclude={"precio"})  # ❌ Use .model_dump()
```

### FastAPI Endpoint

```python
from fastapi import APIRouter, Depends, HTTPException, status
from typing import List
from app.core.deps import get_current_user
from app.models.user import User

router = APIRouter(prefix="/api", tags=["productos"])

@router.get("/productos", response_model=List[ProductoResponse])
async def get_productos(
    skip: int = 0,
    limit: int = 100,
    current_user: User = Depends(get_current_user)
) -> List[ProductoResponse]:
    """
    Retrieve all productos with pagination.
    Requires authentication.
    """
    # Business logic here
    productos = await fetch_productos(skip, limit)
    return productos
```

### SQLAlchemy Model

```python
from sqlalchemy import Column, Integer, String, DateTime, ForeignKey, Index
from sqlalchemy.orm import relationship
from datetime import datetime, UTC
from app.core.database import Base

class Producto(Base):
    __tablename__ = "productos_erp"
    
    id = Column(Integer, primary_key=True, index=True)
    codigo = Column(String(50), nullable=False, unique=True)
    descripcion = Column(String(255), nullable=False)
    costo = Column(Integer, nullable=False)
    marca_id = Column(Integer, ForeignKey("marcas.id"), nullable=True)
    
    # ✅ Use datetime.now(UTC) instead of datetime.utcnow() (deprecated in Python 3.12+)
    created_at = Column(DateTime, default=lambda: datetime.now(UTC))
    updated_at = Column(DateTime, default=lambda: datetime.now(UTC), onupdate=lambda: datetime.now(UTC))
    
    # Relationships
    marca = relationship("Marca", back_populates="productos")
    
    # Indexes
    __table_args__ = (
        Index("idx_producto_codigo", "codigo"),
        Index("idx_producto_marca", "marca_id"),
    )
```

### Alembic Migration

```python
"""add_titulo_ml_to_productos_erp

Revision ID: 5cf5f4b6e839
Revises: previous_revision
Create Date: 2025-01-15 10:30:00

"""
from alembic import op
import sqlalchemy as sa

# revision identifiers
revision = '5cf5f4b6e839'
down_revision = 'previous_revision'
branch_labels = None
depends_on = None

def upgrade():
    op.add_column('productos_erp', sa.Column('titulo_ml', sa.String(255), nullable=True))
    op.create_index('idx_productos_titulo_ml', 'productos_erp', ['titulo_ml'])

def downgrade():
    op.drop_index('idx_productos_titulo_ml', table_name='productos_erp')
    op.drop_column('productos_erp', 'titulo_ml')
```

### Authentication Dependency

```python
from fastapi import Depends, HTTPException, status
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials
from jose import JWTError, jwt
from app.core.config import settings
from app.models.user import User
from app.core.database import SessionLocal

security = HTTPBearer()

async def get_current_user(
    credentials: HTTPAuthorizationCredentials = Depends(security)
) -> User:
    """
    Validate JWT token and return current user.
    Raises 401 if token is invalid or user not found.
    """
    token = credentials.credentials
    try:
        payload = jwt.decode(token, settings.SECRET_KEY, algorithms=["HS256"])
        user_id: int = payload.get("sub")
        if user_id is None:
            raise HTTPException(
                status_code=status.HTTP_401_UNAUTHORIZED,
                detail="Invalid authentication credentials"
            )
    except JWTError:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid authentication credentials"
        )
    
    db = SessionLocal()
    user = db.query(User).filter(User.id == user_id).first()
    db.close()
    
    if user is None:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="User not found"
        )
    
    return user
```

### Permission Check

```python
from app.utils.permisos import tienePermiso

@router.post("/productos", response_model=ProductoResponse)
async def create_producto(
    producto: ProductoCreate,
    current_user: User = Depends(get_current_user)
):
    """Create new producto. Requires 'config' permission."""
    if not tienePermiso(current_user, "config"):
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="No tienes permiso para esta operación"
        )
    
    # Business logic...
    return created_producto
```

---

## NAMING CONVENTIONS

| Entity | Pattern | Example |
|--------|---------|---------|
| Router file | `{domain}.py` | `productos.py`, `ventas.py` |
| Model file | `{entity}.py` | `user.py`, `producto.py` |
| Service file | `{domain}_service.py` | `pricing_service.py` |
| Migration | `YYYYMMDD_description.py` | `20250115_add_titulo_ml.py` |
| Endpoint | `/api/{resource}` | `/api/productos` |
| Database table | `{entity}_erp` or `tb_{entity}` | `productos_erp`, `tb_usuarios` |

---

## COMMON PITFALLS

### Pydantic v2 (AVOID MIXING SYNTAXES)
- ❌ Don't use `class Config:` → Use `model_config = ConfigDict(...)`
- ❌ Don't use `.dict()` → Use `.model_dump()`
- ❌ Don't use `.json()` → Use `.model_dump_json()`
- ❌ Don't use `orm_mode = True` → Use `from_attributes=True` in ConfigDict
- ❌ Don't use `datetime.utcnow()` → Use `datetime.now(UTC)` (Python 3.12+)
- ❌ Don't mix v1 and v2 syntax → Pick one and be consistent

### Backend
- ❌ Don't query DB in loops → Use `joinedload()` or bulk operations
- ❌ Don't return DB models directly → Use Pydantic response models
- ❌ Don't hardcode config → Use environment variables
- ❌ Don't skip migrations → Always run `alembic upgrade head`
- ❌ Don't use `select *` → Specify columns explicitly
- ❌ Don't forget indexes → Add indexes for foreign keys and filters

---

## COMMANDS

```bash
# Development
cd backend
source venv/bin/activate  # or activate virtualenv
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

# Database
alembic revision --autogenerate -m "description"
alembic upgrade head
alembic downgrade -1

# Dependencies
pip install -r requirements.txt
pip freeze > requirements.txt
```

---

## QA CHECKLIST

- [ ] All functions have type hints
- [ ] Endpoints have response models
- [ ] Auth/permissions checked where needed
- [ ] No bare `except:` blocks
- [ ] Migrations created for schema changes
- [ ] Indexes added for foreign keys
- [ ] No hardcoded config values
- [ ] No sensitive data in logs
- [ ] Error messages are user-friendly
- [ ] **Pydantic v2 syntax used** (no `.dict()`, `class Config:`, or `datetime.utcnow()`)
- [ ] **No mixed v1/v2 syntax** in new or modified code

---

## REFERENCES

### External
- FastAPI docs: https://fastapi.tiangolo.com
- SQLAlchemy docs: https://docs.sqlalchemy.org
- Alembic docs: https://alembic.sqlalchemy.org
- **Pydantic v2 docs: https://docs.pydantic.dev/latest/**
- **Pydantic v2 Migration Guide: https://docs.pydantic.dev/latest/migration/**

### Internal
- [Backend References](references/README.md) - Links to all internal docs
- [Scripts README](../../backend/scripts/README.md) - Cron jobs and data sync
- [Turbo Routing](../../backend/TURBO_ROUTING_README.md) - Delivery routing logic
- [ML Sync Process](../../backend/app/scripts/README_ML_SYNC.md) - MercadoLibre catalog sync

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sernafernando) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

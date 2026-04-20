---
name: fastapi-project-builder
description: Builds FastAPI projects from beginner to professional level, including project structure, database integration, authentication, testing, and deployment. Use when creating new FastAPI projects, adding features, or improving project structure.
metadata:
  author: ashfaq1192
---

# FastAPI Project Builder Skill

## Overview

This Skill guides you through building FastAPI applications at all levels:
- **Beginner**: Basic routes, simple models, quick start
- **Intermediate**: Database integration, validation, error handling
- **Professional**: Authentication, testing, documentation, deployment

## Quick Start

### 1. Project Structure

Always organize FastAPI projects like this:

```
project/
├── app/
│   ├── __init__.py
│   ├── main.py
│   ├── models/
│   │   ├── __init__.py
│   │   ├── schemas.py
│   │   └── database.py
│   ├── routes/
│   │   ├── __init__.py
│   │   └── tasks.py
│   ├── database.py
│   ├── config.py
│   └── dependencies.py
├── tests/
│   ├── __init__.py
│   ├── test_api.py
│   └── test_models.py
├── requirements.txt
├── pyproject.toml
├── .env.example
└── README.md
```

### 2. Core Files Template

**`app/main.py`** - Entry point:
```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from app.routes import tasks
from app.config import settings

app = FastAPI(
    title="Task Management API",
    description="Professional task management system",
    version="1.0.0"
)

# CORS configuration
app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.ALLOWED_ORIGINS,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Include routers
app.include_router(tasks.router, prefix="/api/v1/tasks", tags=["tasks"])

@app.get("/health")
async def health_check():
    return {"status": "healthy"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run("app.main:app", host="0.0.0.0", port=8000, reload=True)
```

**`app/models/schemas.py`** - Pydantic models:
```python
from pydantic import BaseModel, Field
from typing import Optional
from datetime import datetime

class TaskBase(BaseModel):
    title: str = Field(..., min_length=1, max_length=200)
    description: Optional[str] = Field(None, max_length=5000)
    status: str = Field(default="todo", pattern="^(todo|in_progress|done)$")
    priority: str = Field(default="medium", pattern="^(low|medium|high)$")

class TaskCreate(TaskBase):
    pass

class TaskUpdate(BaseModel):
    title: Optional[str] = Field(None, min_length=1, max_length=200)
    description: Optional[str] = None
    status: Optional[str] = Field(None, pattern="^(todo|in_progress|done)$")
    priority: Optional[str] = Field(None, pattern="^(low|medium|high)$")

class Task(TaskBase):
    id: int
    created_at: datetime
    updated_at: datetime

    class Config:
        from_attributes = True
```

**`app/config.py`** - Configuration:
```python
from pydantic_settings import BaseSettings
from typing import List

class Settings(BaseSettings):
    API_TITLE: str = "Task Management API"
    DEBUG: bool = False
    DATABASE_URL: str = "sqlite:///./tasks.db"
    ALLOWED_ORIGINS: List[str] = ["http://localhost:3000"]
    SECRET_KEY: str = "dev-secret-key-change-in-production"
    ALGORITHM: str = "HS256"
    ACCESS_TOKEN_EXPIRE_MINUTES: int = 30

    class Config:
        env_file = ".env"

settings = Settings()
```

**`requirements.txt`**:
```
fastapi==0.109.0
uvicorn[standard]==0.27.0
sqlalchemy==2.0.23
pydantic==2.5.0
pydantic-settings==2.1.0
python-multipart==0.0.6
pytest==7.4.3
httpx==0.25.2
python-jose[cryptography]==3.3.0
passlib[bcrypt]==1.7.4
```

### 3. Database Integration

**`app/database.py`**:
```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, declarative_base
from app.config import settings

engine = create_engine(
    settings.DATABASE_URL,
    connect_args={"check_same_thread": False} if "sqlite" in settings.DATABASE_URL else {}
)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

**`app/models/database.py`** (SQLAlchemy models):
```python
from sqlalchemy import Column, Integer, String, DateTime, Text
from datetime import datetime
from app.database import Base

class TaskDB(Base):
    __tablename__ = "tasks"

    id = Column(Integer, primary_key=True, index=True)
    title = Column(String(200), nullable=False)
    description = Column(Text, nullable=True)
    status = Column(String(50), default="todo")
    priority = Column(String(50), default="medium")
    created_at = Column(DateTime, default=datetime.utcnow)
    updated_at = Column(DateTime, default=datetime.utcnow, onupdate=datetime.utcnow)
```

### 4. API Routes

**`app/routes/tasks.py`**:
```python
from fastapi import APIRouter, Depends, HTTPException, Query
from sqlalchemy.orm import Session
from typing import List
from app.models.schemas import Task, TaskCreate, TaskUpdate
from app.models.database import TaskDB
from app.database import get_db

router = APIRouter()

@router.get("/", response_model=List[Task])
async def list_tasks(
    skip: int = Query(0, ge=0),
    limit: int = Query(10, ge=1, le=100),
    status: str = Query(None),
    db: Session = Depends(get_db)
):
    query = db.query(TaskDB)
    if status:
        query = query.filter(TaskDB.status == status)
    return query.offset(skip).limit(limit).all()

@router.post("/", response_model=Task)
async def create_task(task: TaskCreate, db: Session = Depends(get_db)):
    db_task = TaskDB(**task.dict())
    db.add(db_task)
    db.commit()
    db.refresh(db_task)
    return db_task

@router.get("/{task_id}", response_model=Task)
async def get_task(task_id: int, db: Session = Depends(get_db)):
    task = db.query(TaskDB).filter(TaskDB.id == task_id).first()
    if not task:
        raise HTTPException(status_code=404, detail="Task not found")
    return task

@router.put("/{task_id}", response_model=Task)
async def update_task(task_id: int, task: TaskUpdate, db: Session = Depends(get_db)):
    db_task = db.query(TaskDB).filter(TaskDB.id == task_id).first()
    if not db_task:
        raise HTTPException(status_code=404, detail="Task not found")
    update_data = task.dict(exclude_unset=True)
    for field, value in update_data.items():
        setattr(db_task, field, value)
    db.commit()
    db.refresh(db_task)
    return db_task

@router.delete("/{task_id}", status_code=204)
async def delete_task(task_id: int, db: Session = Depends(get_db)):
    db_task = db.query(TaskDB).filter(TaskDB.id == task_id).first()
    if not db_task:
        raise HTTPException(status_code=404, detail="Task not found")
    db.delete(db_task)
    db.commit()
```

### 5. Testing Best Practices

**`tests/test_api.py`**:
```python
import pytest
from fastapi.testclient import TestClient
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from app.main import app
from app.database import Base, get_db
from app.models.database import TaskDB

# Use in-memory SQLite for testing
SQLALCHEMY_DATABASE_URL = "sqlite:///./test.db"
engine = create_engine(SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False})
TestingSessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base.metadata.create_all(bind=engine)

def override_get_db():
    db = TestingSessionLocal()
    try:
        yield db
    finally:
        db.close()

app.dependency_overrides[get_db] = override_get_db
client = TestClient(app)

def test_health_check():
    response = client.get("/health")
    assert response.status_code == 200

def test_create_task():
    response = client.post(
        "/api/v1/tasks/",
        json={"title": "Test task", "description": "Test description"}
    )
    assert response.status_code == 200
    assert response.json()["title"] == "Test task"

def test_get_task():
    # Create first
    create_response = client.post(
        "/api/v1/tasks/",
        json={"title": "Test task"}
    )
    task_id = create_response.json()["id"]

    # Get it
    response = client.get(f"/api/v1/tasks/{task_id}")
    assert response.status_code == 200
    assert response.json()["id"] == task_id
```

## Professional-Level Patterns

### Authentication with JWT

```python
from datetime import datetime, timedelta
from typing import Optional
from jose import JWTError, jwt
from passlib.context import CryptContext
from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

def verify_password(plain_password: str, hashed_password: str) -> bool:
    return pwd_context.verify(plain_password, hashed_password)

def get_password_hash(password: str) -> str:
    return pwd_context.hash(password)

def create_access_token(data: dict, expires_delta: Optional[timedelta] = None):
    to_encode = data.copy()
    expire = datetime.utcnow() + (expires_delta or timedelta(minutes=15))
    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, settings.SECRET_KEY, algorithm=settings.ALGORITHM)

async def get_current_user(token: str = Depends(oauth2_scheme)):
    credentials_exception = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
        headers={"WWW-Authenticate": "Bearer"},
    )
    try:
        payload = jwt.decode(token, settings.SECRET_KEY, algorithms=[settings.ALGORITHM])
        username: str = payload.get("sub")
        if username is None:
            raise credentials_exception
    except JWTError:
        raise credentials_exception
    return username
```

### Custom Error Handlers

```python
from fastapi import Request, status
from fastapi.responses import JSONResponse
from fastapi.exceptions import RequestValidationError

@app.exception_handler(RequestValidationError)
async def validation_exception_handler(request: Request, exc: RequestValidationError):
    return JSONResponse(
        status_code=status.HTTP_422_UNPROCESSABLE_ENTITY,
        content={"detail": exc.errors(), "body": exc.body}
    )

class TaskNotFound(HTTPException):
    def __init__(self, task_id: int):
        super().__init__(
            status_code=404,
            detail=f"Task with id {task_id} not found"
        )
```

### Logging Setup

```python
import logging
import sys

# Configure logging
logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s [%(levelname)s] %(name)s: %(message)s",
    handlers=[
        logging.StreamHandler(sys.stdout)
    ]
)

logger = logging.getLogger(__name__)

# Use in routes
@router.post("/")
async def create_task(task: TaskCreate, db: Session = Depends(get_db)):
    logger.info(f"Creating new task: {task.title}")
    # ... rest of the function
```

### Pagination Helper

```python
from typing import Generic, TypeVar, List
from pydantic import BaseModel

T = TypeVar('T')

class PaginatedResponse(BaseModel, Generic[T]):
    items: List[T]
    total: int
    page: int
    page_size: int
    pages: int

def paginate(query, page: int, page_size: int):
    total = query.count()
    items = query.offset((page - 1) * page_size).limit(page_size).all()
    return PaginatedResponse(
        items=items,
        total=total,
        page=page,
        page_size=page_size,
        pages=(total + page_size - 1) // page_size
    )
```

### Background Tasks

```python
from fastapi import BackgroundTasks

def send_email_notification(email: str, message: str):
    # Simulate sending email
    print(f"Sending email to {email}: {message}")

@router.post("/tasks/")
async def create_task_with_notification(
    task: TaskCreate,
    background_tasks: BackgroundTasks,
    db: Session = Depends(get_db)
):
    db_task = TaskDB(**task.dict())
    db.add(db_task)
    db.commit()

    background_tasks.add_task(
        send_email_notification,
        "admin@example.com",
        f"New task created: {task.title}"
    )

    db.refresh(db_task)
    return db_task
```

## Deployment Checklist

1. **Environment Variables**: Use `.env` file, never hardcode secrets
2. **Database Migrations**: Use Alembic for production databases
3. **CORS**: Configure properly for your frontend domain
4. **Rate Limiting**: Add middleware for API rate limiting
5. **Monitoring**: Set up logging and error tracking (Sentry, etc.)
6. **Docker**: Containerize your application
7. **HTTPS**: Always use HTTPS in production
8. **Database Connection Pooling**: Configure for production load
9. **Testing**: Achieve >80% code coverage
10. **Documentation**: Keep OpenAPI docs up to date

## Running the Application

```bash
# Development
fastapi dev app/main.py

# Production
uvicorn app.main:app --host 0.0.0.0 --port 8000 --workers 4

# With Docker
docker build -t myapi .
docker run -p 8000:8000 myapi
```

## Interactive Documentation

Once running, access:
- Swagger UI: `http://localhost:8000/docs`
- ReDoc: `http://localhost:8000/redoc`
- OpenAPI JSON: `http://localhost:8000/openapi.json`

## References

For more advanced patterns and templates, see:
- [TEMPLATES.md](references/TEMPLATES.md) - Additional project templates
- [API_PATTERNS.md](references/API_PATTERNS.md) - Design patterns and best practices

## Common Commands

```bash
# Install dependencies
pip install -r requirements.txt

# Run tests
pytest tests/ -v

# Run with coverage
pytest tests/ --cov=app --cov-report=html

# Format code
black app/ tests/

# Lint code
ruff check app/ tests/

# Create database tables
python -c "from app.database import Base, engine; Base.metadata.create_all(bind=engine)"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ashfaq1192) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

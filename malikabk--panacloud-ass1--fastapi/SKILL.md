---
name: fastapi
description: Comprehensive FastAPI development assistance including project setup, route creation, database integration, authentication, and deployment. Use when Claude needs to work with FastAPI projects for: (1) Creating new FastAPI applications, (2) Adding API endpoints and routes, (3) Setting up database connections with SQLAlchemy/SQLModel, (4) Implementing authentication and security, (5) Creating Pydantic models, (6) Docker deployment, or any other FastAPI development tasks Use when this capability is needed.
metadata:
  author: malikabk
---

# FastAPI Development Assistant

## Overview

This skill provides comprehensive assistance for developing applications with FastAPI, a modern, fast web framework for building APIs with Python 3.7+ based on standard Python type hints. FastAPI provides data validation, serialization, interactive API documentation (Swagger UI and ReDoc), and automatic OpenAPI schema generation.

## Quick Start

When creating a new FastAPI project, follow these steps:

1. Use UV to initialize the project: `uv init`
2. Add FastAPI dependencies: `uv add fastapi uvicorn`
3. Create your main application file (typically `main.py` or `app.py`)
4. Run with: `uv run uvicorn main:app --reload`

## Core Capabilities

### 1. Project Setup
- Initialize new FastAPI projects with proper structure
- Set up dependencies with UV
- Configure development environments
- Create basic application templates

### 2. Route Creation
- Define GET, POST, PUT, DELETE endpoints
- Implement path and query parameters
- Handle request/response models with Pydantic
- Add validation and error handling

### 3. Database Integration
- Set up SQLAlchemy or SQLModel connections
- Create database models and schemas
- Implement CRUD operations
- Configure connection pooling and sessions

### 4. Authentication & Security
- JWT token implementation
- OAuth2 with password flow
- API key authentication
- Password hashing with bcrypt

### 5. Deployment
- Docker containerization
- Environment configuration
- Production server setup with Uvicorn

## Project Structure Templates

### Basic Application Template
```
my-fastapi-app/
├── app/
│   ├── __init__.py
│   ├── main.py          # Application instance and routes
│   ├── models.py        # Pydantic models
│   ├── database.py      # Database connection/session
│   └── routers/         # API route modules
├── requirements.txt     # Dependencies (managed by UV)
├── .env                # Environment variables
└── docker-compose.yml  # Docker configuration
```

## Common Code Patterns

### Basic FastAPI Application
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"Hello": "World"}

@app.get("/items/{item_id}")
def read_item(item_id: int, q: str = None):
    return {"item_id": item_id, "q": q}
```

### Type Hints for Path/Query Parameters (Best Practice)
Always use explicit type hints for path and query parameters to ensure:
- Better validation and error handling
- Automatic documentation generation
- Improved IDE support and code completion
- Type safety during development

```python
from fastapi import FastAPI
from typing import Optional

app = FastAPI()

@app.get("/items/{item_id}")
def get_item(item_id: int, q: Optional[str] = None):  # Good: type hints used
    return {"item_id": item_id, "q": q}
```

### Always Return Dictionaries (Best Practice)
Never return `None` from API endpoints. Always return meaningful dictionary responses to ensure consistent JSON responses and prevent `null` responses in the API.

```python
@app.get("/items/{item_id}")
def get_item(item_id: int):
    item = {"id": item_id, "name": f"Item {item_id}"}  # Create the item
    return item  # Good: always return a dictionary, not None
```

### Descriptive Function Names (Best Practice)
Use function names that clearly describe what the endpoint does to improve code readability, maintainability, and debugging.

```python
@app.get("/users/{user_id}")
def get_user_profile(user_id: int):  # Good: descriptive name matching purpose
    # Returns user profile information
    pass

@app.post("/users")
def create_new_user(user_data: dict):  # Good: descriptive name matching purpose
    # Creates a new user
    pass
```

### Pydantic Models
```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    description: str = None
    price: float
    tax: float = None
```

### Database Model with SQLAlchemy
```python
from sqlalchemy import Column, Integer, String
from database import Base

class User(Base):
    __tablename__ = "users"

    id = Column(Integer, primary_key=True, index=True)
    email = Column(String, unique=True, index=True)
    name = Column(String)
```

## Testing with Pytest

### TestClient Usage (Best Practice)
Use FastAPI's TestClient for testing your API endpoints. This provides an easy way to simulate HTTP requests without starting a server.

```python
from fastapi.testclient import TestClient
from app.main import app

client = TestClient(app)

def test_read_main():
    response = client.get("/")
    assert response.status_code == 200
    assert response.json() == {"message": "Welcome to FastAPI!"}

def test_create_item():
    # Test POST request
    response = client.post("/items/", json={
        "name": "Test Item",
        "price": 10.50,
        "description": "A test item"
    })
    assert response.status_code == 200
    data = response.json()
    assert data["name"] == "Test Item"
    assert data["price"] == 10.50
```

### Pytest Fixtures for Test Setup and Teardown
Use pytest fixtures to set up test dependencies, database connections, and clean up after tests.

```python
import pytest
from fastapi.testclient import TestClient
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from app.main import app
from app.database import Base, get_db
from app.models import Item  # Adjust import path as needed

# Test database setup
SQLALCHEMY_DATABASE_URL = "sqlite:///./test.db"
engine = create_engine(SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False})
TestingSessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

# Create tables
Base.metadata.create_all(bind=engine)

@pytest.fixture(scope="module")
def client():
    """Create a test client for the application."""
    with TestClient(app) as c:
        yield c

@pytest.fixture(scope="function")
def db_session():
    """Create a database session for each test."""
    connection = engine.connect()
    transaction = connection.begin()
    session = TestingSessionLocal(bind=connection)

    yield session

    session.close()
    transaction.rollback()
    connection.close()

def test_create_item_with_db(client, db_session):
    """Test creating an item with database integration."""
    # Override the get_db dependency with our test session
    app.dependency_overrides[get_db] = lambda: db_session

    response = client.post("/items/", json={
        "name": "Database Test Item",
        "price": 20.99,
        "description": "An item created for testing"
    })

    assert response.status_code == 200
    data = response.json()
    assert data["name"] == "Database Test Item"

    # Clean up overrides
    app.dependency_overrides.clear()
```

### conftest.py Organization
Create a `conftest.py` file to centralize your test fixtures and configuration:

```python
# conftest.py
import pytest
from fastapi.testclient import TestClient
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from app.main import app
from app.database import Base, get_db

# Use an in-memory SQLite database for tests
TEST_DATABASE_URL = "sqlite:///./test.db"

@pytest.fixture(scope="session")
def test_engine():
    """Create a test database engine."""
    engine = create_engine(TEST_DATABASE_URL, connect_args={"check_same_thread": False})
    Base.metadata.create_all(bind=engine)
    yield engine
    engine.dispose()

@pytest.fixture(scope="function")
def test_db_session(test_engine):
    """Create a test database session."""
    connection = test_engine.connect()
    transaction = connection.begin()
    Session = sessionmaker(bind=connection)
    session = Session()

    yield session

    session.close()
    transaction.rollback()
    connection.close()

@pytest.fixture(scope="function")
def override_get_db(test_db_session):
    """Override the get_db dependency."""
    def _get_db():
        try:
            yield test_db_session
        finally:
            pass

    app.dependency_overrides[get_db] = _get_db
    yield
    app.dependency_overrides.clear()

@pytest.fixture(scope="module")
def client():
    """Create a test client for the application."""
    with TestClient(app) as test_client:
        yield test_client

# Additional fixtures for authentication, etc.
@pytest.fixture
def authenticated_client(client):
    """Return an authenticated test client."""
    # Example: Add authentication headers or cookies
    client.headers.update({"Authorization": "Bearer test_token"})
    return client
```

### Red-Green Testing Cycle for TDD
Follow the Test-Driven Development cycle when building FastAPI applications:

1. **RED**: Write a failing test that describes the desired functionality
   - Create a test that verifies the expected behavior
   - The test should initially fail because the functionality doesn't exist yet

2. **GREEN**: Write minimal code to make the test pass
   - Implement just enough functionality to satisfy the test
   - Don't worry about optimization or refactoring yet

3. **REFACTOR**: Improve the code while keeping tests passing
   - Optimize the implementation
   - Improve readability and maintainability
   - Ensure all tests still pass

**Example TDD Cycle:**

```python
# 1. RED: Write a failing test first
def test_get_item_by_id():
    """Test retrieving an item by its ID."""
    response = client.get("/items/1")
    assert response.status_code == 200
    assert response.json()["id"] == 1

# 2. GREEN: Write minimal code to make it pass
@app.get("/items/{item_id}")
def get_item(item_id: int):
    # Minimal implementation to pass test
    return {"id": item_id, "name": "Default Item"}

# 3. REFACTOR: Improve the implementation
@app.get("/items/{item_id}", response_model=Item)
def get_item(item_id: int, db: Session = Depends(get_db)):
    """Get an item by its ID."""
    item = db.query(ItemModel).filter(ItemModel.id == item_id).first()
    if not item:
        raise HTTPException(status_code=404, detail="Item not found")
    return item
```

### Common Test Patterns
- Test all HTTP methods (GET, POST, PUT, DELETE)
- Test error cases and edge conditions
- Test authentication and authorization
- Test data validation
- Mock external services to isolate API logic

## Resources

This skill includes resources for different aspects of FastAPI development:

### scripts/
Python and shell scripts for common FastAPI operations.

**Examples:**
- `create_fastapi_app.py` - Script to generate new FastAPI project structure
- `add_router.py` - Script to add new router modules
- `setup_database.py` - Script to initialize database connections
- `create_test_scaffold.py` - Script to generate test structure with fixtures

### references/
Detailed documentation and reference materials for FastAPI features.

**Examples:**
- `api_patterns.md` - Common API design patterns
- `database_patterns.md` - SQLAlchemy/SQLModel best practices
- `security_patterns.md` - Authentication and security implementations
- `deployment_patterns.md` - Docker and production deployment guides
- `testing_patterns.md` - Pytest and TestClient best practices

### assets/
Project templates and boilerplate code for common FastAPI setups.

**Examples:**
- `templates/basic-app/` - Basic FastAPI application template
- `templates/auth-app/` - FastAPI app with authentication
- `templates/full-stack/` - Full-stack app with database integration
- `templates/tested-app/` - App with comprehensive test suite

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/malikabk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

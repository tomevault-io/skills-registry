---
name: api-utils
description: Comprehensive API development utilities and helper functions for FastAPI, SQLModel, and pytest. Use when Claude needs to work with API development for: (1) Creating common API patterns and utilities, (2) Implementing error handling and validation helpers, (3) Setting up authentication and authorization utilities, (4) Creating pagination and filtering helpers, (5) Building response formatting utilities, or any other API utility development operations. Use when this capability is needed.
metadata:
  author: malikabk
---

# API Development Utilities Assistant

## Overview

This skill provides comprehensive utilities and helper functions for building robust APIs with FastAPI, SQLModel, and pytest. It includes common patterns, utilities, and best practices that can be reused across different API projects to accelerate development and maintain consistency.

## Core Capabilities

### 1. Response Utilities
- Standardized response formatting
- Error response patterns
- Pagination helpers
- Success/failure response templates

### 2. Validation and Error Handling
- Custom exception classes
- Validation utilities
- Error response formatting
- Input sanitization helpers

### 3. Authentication Utilities
- JWT token handling
- User authentication helpers
- Permission checking utilities
- Session management

### 4. Database Utilities
- Session management helpers
- Query optimization utilities
- Transaction management
- Connection pooling

## Response Utilities

### Standard Response Models
```python
from pydantic import BaseModel
from typing import Generic, TypeVar, Optional, List
from enum import Enum

T = TypeVar('T')

class ResponseStatus(str, Enum):
    SUCCESS = "success"
    ERROR = "error"

class APIResponse(BaseModel, Generic[T]):
    status: ResponseStatus
    message: str
    data: Optional[T] = None
    error_code: Optional[str] = None

class PaginatedResponse(BaseModel, Generic[T]):
    items: List[T]
    total: int
    page: int
    limit: int
    pages: int

# Usage examples:
# success_response = APIResponse[UserRead](status=ResponseStatus.SUCCESS, message="User created", data=user)
# error_response = APIResponse(status=ResponseStatus.ERROR, message="User not found", error_code="USER_NOT_FOUND")
```

### Response Helper Functions
```python
from fastapi import status
from typing import Optional

def success_response(data, message: str = "Success", status_code: int = 200):
    """Create a standardized success response."""
    return APIResponse(
        status=ResponseStatus.SUCCESS,
        message=message,
        data=data
    ), status_code

def error_response(message: str, error_code: str = "GENERIC_ERROR", status_code: int = 400):
    """Create a standardized error response."""
    return APIResponse(
        status=ResponseStatus.ERROR,
        message=message,
        error_code=error_code
    ), status_code

def paginated_response(items, total, page, limit):
    """Create a paginated response."""
    pages = (total + limit - 1) // limit  # Ceiling division
    return PaginatedResponse(
        items=items,
        total=total,
        page=page,
        limit=limit,
        pages=pages
    )
```

## Error Handling Utilities

### Custom Exception Classes
```python
class APIException(Exception):
    """Base exception for API errors."""
    def __init__(self, message: str, error_code: str = "GENERIC_ERROR", status_code: int = 400):
        self.message = message
        self.error_code = error_code
        self.status_code = status_code
        super().__init__(self.message)

class ResourceNotFoundError(APIException):
    """Raised when a requested resource is not found."""
    def __init__(self, resource_type: str, resource_id: int):
        super().__init__(
            message=f"{resource_type} with ID {resource_id} not found",
            error_code="RESOURCE_NOT_FOUND",
            status_code=status.HTTP_404_NOT_FOUND
        )

class ValidationError(APIException):
    """Raised when validation fails."""
    def __init__(self, message: str):
        super().__init__(
            message=message,
            error_code="VALIDATION_ERROR",
            status_code=status.HTTP_422_UNPROCESSABLE_ENTITY
        )

class UnauthorizedError(APIException):
    """Raised when authentication/authorization fails."""
    def __init__(self, message: str = "Unauthorized"):
        super().__init__(
            message=message,
            error_code="UNAUTHORIZED",
            status_code=status.HTTP_401_UNAUTHORIZED
        )
```

### Exception Handler
```python
from fastapi import Request
from fastapi.responses import JSONResponse

async def api_exception_handler(request: Request, exc: APIException):
    """Global exception handler for API exceptions."""
    return JSONResponse(
        status_code=exc.status_code,
        content={
            "status": "error",
            "message": exc.message,
            "error_code": exc.error_code
        }
    )

# Register the exception handler in your FastAPI app:
# app.add_exception_handler(APIException, api_exception_handler)
```

## Authentication Utilities

### JWT Token Utilities
```python
from datetime import datetime, timedelta
import jwt
from typing import Optional
from fastapi import HTTPException, status, Depends
from fastapi.security import HTTPBearer

security = HTTPBearer()

SECRET_KEY = "your-secret-key"  # In production, use environment variables
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30

def create_access_token(data: dict, expires_delta: Optional[timedelta] = None):
    """Create a JWT access token."""
    to_encode = data.copy()
    if expires_delta:
        expire = datetime.utcnow() + expires_delta
    else:
        expire = datetime.utcnow() + timedelta(minutes=15)

    to_encode.update({"exp": expire})
    encoded_jwt = jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
    return encoded_jwt

def verify_token(token: str):
    """Verify a JWT token and return the payload."""
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        return payload
    except jwt.ExpiredSignatureError:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Token has expired"
        )
    except jwt.JWTError:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Could not validate credentials"
        )

def get_current_user(token: str = Depends(security)):
    """Dependency to get current user from token."""
    payload = verify_token(token.credentials)
    user_id: int = payload.get("sub")
    if user_id is None:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Could not validate credentials"
        )
    return user_id
```

## Database Utilities

### Session Management
```python
from contextlib import contextmanager
from sqlmodel import Session, create_engine
from typing import Generator

DATABASE_URL = "sqlite:///./api_database.db"
engine = create_engine(DATABASE_URL)

@contextmanager
def get_db_session() -> Generator[Session, None, None]:
    """Context manager for database sessions."""
    with Session(engine) as session:
        try:
            yield session
            session.commit()
        except Exception:
            session.rollback()
            raise
        finally:
            session.close()

def get_session() -> Generator[Session, None, None]:
    """FastAPI dependency for database sessions."""
    with Session(engine) as session:
        yield session
```

### Query Utilities
```python
from sqlmodel import select, func
from typing import Type, TypeVar, List, Optional
from sqlalchemy.exc import NoResultFound

T = TypeVar('T')

def get_by_id(model_class: Type[T], id: int, session) -> Optional[T]:
    """Get a record by ID."""
    try:
        statement = select(model_class).where(model_class.id == id)
        return session.exec(statement).one()
    except NoResultFound:
        return None

def get_all_paginated(model_class: Type[T], session, skip: int = 0, limit: int = 100) -> tuple[List[T], int]:
    """Get all records with pagination."""
    count_statement = select(func.count(model_class.id))
    total = session.exec(count_statement).one()

    statement = select(model_class).offset(skip).limit(limit)
    items = session.exec(statement).all()

    return items, total

def delete_by_id(model_class: Type[T], id: int, session) -> bool:
    """Delete a record by ID."""
    item = get_by_id(model_class, id, session)
    if item:
        session.delete(item)
        session.commit()
        return True
    return False
```

## Pagination Utilities

### Pagination Parameters
```python
from fastapi import Query

class PaginationParams:
    def __init__(
        self,
        skip: int = Query(default=0, ge=0, description="Number of records to skip"),
        limit: int = Query(default=100, ge=1, le=1000, description="Maximum number of records to return")
    ):
        self.skip = skip
        self.limit = limit

# Usage in endpoints:
# @app.get("/items/")
# def read_items(pagination: PaginationParams = Depends(PaginationParams)):
#     items, total = get_all_paginated(Item, session, pagination.skip, pagination.limit)
#     return paginated_response(items, total, pagination.skip // pagination.limit + 1, pagination.limit)
```

## Validation Utilities

### Custom Validators
```python
from pydantic import validator, root_validator
from typing import Any

def validate_email(v: str) -> str:
    """Validate email format."""
    if "@" not in v:
        raise ValueError("Invalid email format")
    return v.lower().strip()

def validate_phone(v: str) -> str:
    """Validate phone number format."""
    import re
    # Remove all non-digit characters
    digits_only = re.sub(r'\D', '', v)
    if len(digits_only) < 10:
        raise ValueError("Phone number must have at least 10 digits")
    return digits_only

def validate_url(v: str) -> str:
    """Validate URL format."""
    if not v.startswith(("http://", "https://")):
        raise ValueError("URL must start with http:// or https://")
    return v
```

## Resources

This skill includes resources for different aspects of API utility development:

### scripts/
Python and shell scripts for common API utility operations.

**Examples:**
- `generate_response_models.py` - Script to generate standard response models
- `create_exception_handlers.py` - Script to generate exception handling utilities
- `setup_auth_utils.py` - Script to generate authentication utilities
- `create_pagination_helpers.py` - Script to generate pagination utilities

### references/
Detailed documentation and reference materials for API utility patterns.

**Examples:**
- `response_patterns.md` - Standard response formatting patterns
- `error_handling.md` - Comprehensive error handling strategies
- `authentication_patterns.md` - Authentication and authorization patterns
- `validation_rules.md` - Data validation and sanitization patterns

### assets/
Project templates and boilerplate code for common API utility setups.

**Examples:**
- `templates/api-utilities/` - Complete API utilities template
- `templates/auth-service/` - Authentication service template
- `templates/error-handling/` - Error handling utilities template

**Any unneeded directories can be deleted.** Not every skill requires all three types of resources.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/malikabk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

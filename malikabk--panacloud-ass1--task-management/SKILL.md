---
name: task-management
description: Comprehensive task management API development assistance using FastAPI, SQLModel, and pytest. Use when Claude needs to work with task management systems for: (1) Creating task models and database schemas, (2) Implementing CRUD operations for tasks, (3) Building RESTful APIs with proper validation, (4) Testing task management functionality, (5) Integrating authentication and authorization for tasks, or any other task management development operations. Use when this capability is needed.
metadata:
  author: malikabk
---

# Task Management API Development Assistant

## Overview

This skill provides comprehensive assistance for developing task management APIs using the FastAPI, SQLModel, and pytest stack. It covers everything from database design to API implementation and testing, following best practices for building robust and scalable task management systems.

## Core Capabilities

### 1. Task Model Design
- Define task database models with proper fields and relationships
- Implement validation rules for task properties
- Design status tracking and categorization systems
- Handle due dates, priorities, and assignments

### 2. API Endpoint Implementation
- Create RESTful endpoints for task operations
- Implement proper request/response validation
- Handle authentication and authorization
- Design pagination and filtering capabilities

### 3. Database Operations
- Set up database connections and session management
- Implement CRUD operations for tasks
- Handle complex queries and filtering
- Manage database migrations and schema evolution

### 4. Testing and Quality Assurance
- Create comprehensive test suites for task functionality
- Implement fixture patterns for test data
- Test edge cases and error conditions
- Validate API contracts and responses

## Task Model Design

### Basic Task Model
```python
from sqlmodel import SQLModel, Field
from datetime import datetime
from enum import Enum
from typing import Optional

class TaskStatus(str, Enum):
    TODO = "todo"
    IN_PROGRESS = "in_progress"
    DONE = "done"
    CANCELLED = "cancelled"

class TaskPriority(str, Enum):
    LOW = "low"
    MEDIUM = "medium"
    HIGH = "high"
    URGENT = "urgent"

class TaskBase(SQLModel):
    title: str = Field(min_length=1, max_length=200)
    description: Optional[str] = Field(default=None, max_length=1000)
    status: TaskStatus = Field(default=TaskStatus.TODO)
    priority: TaskPriority = Field(default=TaskPriority.MEDIUM)
    due_date: Optional[datetime] = Field(default=None)
    completed_at: Optional[datetime] = Field(default=None)

class Task(TaskBase, table=True):
    id: Optional[int] = Field(default=None, primary_key=True)
    created_at: datetime = Field(default_factory=datetime.utcnow)
    updated_at: datetime = Field(default_factory=datetime.utcnow)

class TaskCreate(TaskBase):
    pass

class TaskUpdate(SQLModel):
    title: Optional[str] = Field(default=None, min_length=1, max_length=200)
    description: Optional[str] = Field(default=None, max_length=1000)
    status: Optional[TaskStatus] = None
    priority: Optional[TaskPriority] = None
    due_date: Optional[datetime] = None

class TaskRead(TaskBase):
    id: int
    created_at: datetime
    updated_at: datetime
```

## API Endpoint Patterns

### Task CRUD Endpoints
```python
from fastapi import FastAPI, HTTPException, Query, Depends
from sqlmodel import Session, select, desc
from typing import List, Optional
from datetime import datetime

app = FastAPI()

@app.post("/tasks/", response_model=TaskRead)
def create_task(task: TaskCreate, session: Session = Depends(get_session)):
    """Create a new task."""
    db_task = Task.from_orm(task)
    session.add(db_task)
    session.commit()
    session.refresh(db_task)
    return db_task

@app.get("/tasks/{task_id}", response_model=TaskRead)
def read_task(task_id: int, session: Session = Depends(get_session)):
    """Get a specific task by ID."""
    task = session.get(Task, task_id)
    if not task:
        raise HTTPException(status_code=404, detail="Task not found")
    return task

@app.get("/tasks/", response_model=List[TaskRead])
def read_tasks(
    skip: int = Query(default=0, ge=0),
    limit: int = Query(default=100, ge=1, le=100),
    status: Optional[TaskStatus] = Query(default=None),
    priority: Optional[TaskPriority] = Query(default=None),
    session: Session = Depends(get_session)
):
    """Get a list of tasks with optional filtering and pagination."""
    statement = select(Task).offset(skip).limit(limit)

    if status:
        statement = statement.where(Task.status == status)
    if priority:
        statement = statement.where(Task.priority == priority)

    statement = statement.order_by(desc(Task.created_at))

    tasks = session.exec(statement).all()
    return tasks

@app.patch("/tasks/{task_id}", response_model=TaskRead)
def update_task(task_id: int, task_update: TaskUpdate, session: Session = Depends(get_session)):
    """Update a specific task."""
    db_task = session.get(Task, task_id)
    if not db_task:
        raise HTTPException(status_code=404, detail="Task not found")

    update_data = task_update.dict(exclude_unset=True)
    for field, value in update_data.items():
        setattr(db_task, field, value)

    db_task.updated_at = datetime.utcnow()
    session.add(db_task)
    session.commit()
    session.refresh(db_task)
    return db_task

@app.delete("/tasks/{task_id}")
def delete_task(task_id: int, session: Session = Depends(get_session)):
    """Delete a specific task."""
    task = session.get(Task, task_id)
    if not task:
        raise HTTPException(status_code=404, detail="Task not found")

    session.delete(task)
    session.commit()
    return {"message": "Task deleted successfully"}
```

## Database Setup and Session Management

### Database Connection
```python
from sqlmodel import create_engine, Session
from sqlalchemy import engine

DATABASE_URL = "sqlite:///./task_management.db"
engine = create_engine(DATABASE_URL, echo=True)

def create_db_and_tables():
    """Create database tables."""
    SQLModel.metadata.create_all(engine)

def get_session():
    """Get database session."""
    with Session(engine) as session:
        yield session
```

## Testing Patterns

### Test Models
```python
import pytest
from sqlmodel import Session, select
from task_management.models import Task, TaskStatus, TaskPriority

@pytest.fixture
def sample_task_data():
    """Sample task data for testing."""
    return {
        "title": "Test Task",
        "description": "A sample task for testing",
        "status": TaskStatus.TODO,
        "priority": TaskPriority.MEDIUM
    }

def test_create_task(client, sample_task_data):
    """Test creating a task."""
    response = client.post("/tasks/", json=sample_task_data)
    assert response.status_code == 200
    data = response.json()
    assert data["title"] == sample_task_data["title"]
    assert data["status"] == sample_task_data["status"]

def test_get_task(client, sample_task_data):
    """Test retrieving a specific task."""
    # First create a task
    create_response = client.post("/tasks/", json=sample_task_data)
    task_id = create_response.json()["id"]

    # Then retrieve it
    response = client.get(f"/tasks/{task_id}")
    assert response.status_code == 200
    data = response.json()
    assert data["id"] == task_id
    assert data["title"] == sample_task_data["title"]

def test_list_tasks(client, sample_task_data):
    """Test listing tasks."""
    # Create multiple tasks
    for i in range(3):
        sample_task_data["title"] = f"Test Task {i}"
        client.post("/tasks/", json=sample_task_data)

    # Retrieve the list
    response = client.get("/tasks/")
    assert response.status_code == 200
    data = response.json()
    assert len(data) >= 3
```

## Advanced Features

### Task Filtering and Search
```python
@app.get("/tasks/search")
def search_tasks(
    q: str = Query(..., min_length=1, description="Search query"),
    status: Optional[TaskStatus] = Query(default=None),
    priority: Optional[TaskPriority] = Query(default=None),
    session: Session = Depends(get_session)
):
    """Search tasks with text matching and filters."""
    statement = select(Task)

    # Apply filters
    if status:
        statement = statement.where(Task.status == status)
    if priority:
        statement = statement.where(Task.priority == priority)

    # Apply text search (simple implementation)
    if q:
        statement = statement.where(Task.title.contains(q) | Task.description.contains(q))

    statement = statement.order_by(desc(Task.created_at))
    tasks = session.exec(statement).all()
    return tasks
```

### Task Statistics
```python
from pydantic import BaseModel
from typing import Dict, Any

class TaskStats(BaseModel):
    total: int
    by_status: Dict[str, int]
    by_priority: Dict[str, int]
    overdue_count: int

@app.get("/tasks/stats", response_model=TaskStats)
def get_task_statistics(session: Session = Depends(get_session)):
    """Get statistics about tasks."""
    all_tasks = session.exec(select(Task)).all()

    total = len(all_tasks)
    by_status = {}
    by_priority = {}
    overdue_count = 0

    for task in all_tasks:
        # Count by status
        status_str = task.status.value
        by_status[status_str] = by_status.get(status_str, 0) + 1

        # Count by priority
        priority_str = task.priority.value
        by_priority[priority_str] = by_priority.get(priority_str, 0) + 1

        # Count overdue tasks
        if task.due_date and task.due_date < datetime.utcnow() and task.status != TaskStatus.DONE:
            overdue_count += 1

    return TaskStats(
        total=total,
        by_status=by_status,
        by_priority=by_priority,
        overdue_count=overdue_count
    )
```

## Resources

This skill includes resources for different aspects of task management API development:

### scripts/
Python and shell scripts for common task management operations.

**Examples:**
- `generate_task_model.py` - Script to generate task model definitions
- `create_task_endpoints.py` - Script to generate CRUD endpoint templates
- `setup_database.py` - Script to initialize database with sample data

### references/
Detailed documentation and reference materials for task management patterns.

**Examples:**
- `api_design_patterns.md` - REST API design best practices for task management
- `validation_rules.md` - Business rules and validation patterns for tasks
- `authentication_patterns.md` - User authentication and task ownership
- `performance_optimization.md` - Query optimization and caching strategies

### assets/
Project templates and boilerplate code for common task management setups.

**Examples:**
- `templates/basic-task-api/` - Basic task management API template
- `templates/advanced-task-api/` - Task API with user management and permissions
- `templates/full-stack/` - Complete task management application with frontend

**Any unneeded directories can be deleted.** Not every skill requires all three types of resources.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/malikabk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

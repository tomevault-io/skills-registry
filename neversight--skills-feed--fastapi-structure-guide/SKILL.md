---
name: fastapi-structure-guide
description: Trigger when the user wants to create a new FastAPI project, add new files/folders (features), refactor existing code, or asks about architectural best practices. This skill enforces a strict layered architecture and specific development workflow. Use when this capability is needed.
metadata:
  author: neversight
---

# FastAPI Structure Guide

## Intent

Use this guide whenever generating code for a FastAPI project, specifically when:

1. **Scaffolding** a brand new project.
2. **Adding a new feature** (e.g., "Add an Order module") which requires creating files across multiple layers.
3. **Refactoring** existing code to meet clean architecture standards.

You must strictly adhere to the **Core Principles**, **Project Structure**, **Development Workflow**, and **Coding Rules** defined below.

---

## I. Core Principles

Before writing a single line of code, adhere to these four guiding principles:

1. **Separation of Concerns:**
* **API Layer:** Responsible only for "Reception" (parsing requests, validating parameters).
* **Service Layer:** Responsible for "Business" (logic calculation, decision making).
* **DB/Model Layer:** Responsible for "Data" (storage access, shape definition).
* *Rule:* Never write business logic inside an API Route function.


2. **Dependency Injection:**
* Do not instantiate components directly (e.g., `service = UserService()`).
* Use FastAPI's `Depends` for injection.
* **Flow:** DB Session -> injected into -> Service -> injected into -> API Route.


3. **Config Centralization:**
* Strictly prohibit hardcoded passwords, keys, or URLs in the code.
* Manage all configurations via **Pydantic Settings** and read from environment variables (`.env`).


4. **Mirrored Testing:**
* The test directory structure must mirror the source code directory structure 1:1.
* Use **SQLite In-Memory** and **Dependency Overrides** to mock the real environment.



---

## II. Recommended Project Structure

Use this standardized directory structure when creating files or folders:

```text
my-fastapi-project/
├── app/                        # Core Application Source
│   ├── __init__.py
│   ├── main.py                 # 🚀 App Entry: Routes mounting, Exception handling
│   ├── api/                    # 🌐 Interface Layer (Routes)
│   │   ├── __init__.py
│   │   └── v1/                 # Version Control
│   │       ├── __init__.py
│   │       ├── api.py          # Router Aggregation (Include Routers)
│   │       └── endpoints/      # Specific Business Endpoints
│   │           ├── __init__.py
│   │           ├── users.py
│   │           └── items.py
│   ├── core/                   # ⚙️ Infrastructure Configuration
│   │   ├── __init__.py
│   │   ├── config.py           # Pydantic Settings (Env Config)
│   │   ├── logging.py          # Logging Config
│   │   └── security.py         # Auth/Hashing Tools
│   ├── db/                     # 🗄️ Database Layer
│   │   ├── __init__.py
│   │   ├── session.py          # DB Connection & Session Factory
│   │   └── tables.py           # SQLAlchemy ORM Definitions (DB Schema)
│   ├── models/                 # 📝 Data Transfer Objects (DTOs)
│   │   ├── __init__.py
│   │   ├── user.py             # Pydantic Models (Request/Response)
│   │   └── item.py
│   └── services/               # 🧠 Business Logic Layer
│       ├── __init__.py
│       ├── base.py             # Optional: Base Service Class
│       ├── user_service.py     # User-related business logic
│       └── item_service.py
├── tests/                      # ✅ Test Cases (Mirrored Structure)
│   ├── __init__.py
│   ├── conftest.py             # Pytest Fixtures (DB override, Client)
│   └── api/
│       └── v1/
│           └── endpoints/
│               └── test_users.py
├── .env                        # 🔐 Local Env Vars (Gitignored)
├── .gitignore
├── docker-compose.yaml         # Local Dev Orchestration
├── Dockerfile                  # Image Build
├── pyproject.toml              # Dependency Management (Recommend uv)
└── README.md

```

### Directory Responsibilities

1. **`app/api/` (Interface Layer)**
* **Responsibility:** Handle HTTP protocol specifics only.
* **Contains:** Path definitions, HTTP methods (GET/POST), status codes, dependency injection declarations.
* **Input/Output:** Pydantic Schemas.
* **Rule:** This layer must be "thin". Functions should strictly be 5-10 lines, calling the Service layer and returning results.


2. **`app/services/` (Business Logic Layer)**
* **Responsibility:** The brain of the application. Handles complex business rules, calculations, and permission checks.
* **Contains:** CRUD operations, 3rd-party API logic, data processing.
* **Input/Output:** Pydantic Schemas or Raw Data -> ORM Objects or Pydantic Schemas.
* **Rule:** Service classes must accept `Session` via `__init__` for easy testing mocks.


3. **`app/models/` (Data Transfer Object Layer)**
* **Responsibility:** Define data "shape" and validation rules for the API.
* **Contains:** Pydantic Models (`BaseModel`).
* **Distinction:** These are *not* DB tables. They are for API input/output (e.g., `UserCreate` vs `UserResponse`).


4. **`app/db/` (Data Access Layer)**
* **Responsibility:** Physical DB connection and Table definitions.
* **Contains:** `session.py` (Engine/SessionLocal), `tables.py` (SQLAlchemy Base models/Columns).


5. **`app/core/` (Cross-Cutting Concerns)**
* **Responsibility:** Infrastructure supporting the app.
* **Contains:** Config loading (`config.py`), logging, security tools. Code here is business-agnostic.



---

## III. Creation Rules (General Development Workflow)

When implementing a new feature, follow these **5 Standard Steps** in order:

### Step A: Define Data Storage (Database Layer)

* **Principle:** Everything starts with data. Determine how the resource looks in the DB.
* **Action:** Add new SQLAlchemy ORM class in `db/tables.py` (or specific file in `db/models/`).
* **Naming:** PascalCase for Class (Singular), snake_case for Table Name (Plural).

### Step B: Define Interaction Contract (Schemas/DTO Layer)

* **Principle:** Define how data moves over the network. Validate Input (Create/Update) and Normalize Output (Response).
* **Action:** Create a new file in `models/`.
* **Naming:** `resource_name.py`. Typically includes `Create`, `Update`, `Response` variants.

### Step C: Implement Business Logic (Service Layer)

* **Principle:** Write the "Verbs". This is the bridge between DB and API.
* **Action:** Create a new file in `services/`.
* **Naming:** `resource_name_service.py`. Class name ends with `Service`.

### Step D: Expose API Interface (API Layer)

* **Principle:** Define the entry point for external access.
* **Action:** Create a new file in `api/v1/endpoints/`.
* **Naming:** `resource_names.py` (Plural) to reflect RESTful style.

### Step E: Registration & Wiring

* **Principle:** New router files are isolated by default; they must be explicitly registered.
* **Action:** Modify `api/v1/api.py` to include the new router.

---

## IV. Coding Rules

### Rule 1: API Routes Must Be "Dumb"

**❌ Wrong (Logic Leakage):**

```python
@router.post("/users")
def create_user(user: UserCreate, db: Session = Depends(get_db)):
    # Error: Logic and DB ops directly in route
    hashed_password = hash_pw(user.password)
    db_user = User(email=user.email, password=hashed_password)
    db.add(db_user)
    db.commit()
    return db_user

```

**✅ Correct (Call Service):**

```python
@router.post("/users")
def create_user(
    user: UserCreate, 
    service: UserService = Depends(get_user_service) # Injected Service
):
    # Correct: Only forwards the request
    return service.create_user(user)

```

### Rule 2: Service Layer Must Use Dependency Injection

Service classes should not create the DB Session themselves; they must receive it in `__init__`.

```python
class UserService:
    def __init__(self, session: Session):
        self.session = session  # ✅ Dependency Injection

    def create_user(self, data: UserCreate):
        # Business logic...
        pass

```

### Rule 3: Config Must Use Pydantic

Never use `os.getenv("KEY")` scattered in the code.

```python
# app/core/config.py
class Settings(BaseSettings):
    DB_URL: str
    SECRET_KEY: str

settings = Settings()

# Usage in other files
from app.core.config import settings
print(settings.DB_URL)

```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

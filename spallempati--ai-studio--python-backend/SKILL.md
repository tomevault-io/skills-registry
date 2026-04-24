---
name: python-backend
description: | Use when this capability is needed.
metadata:
  author: spallempati
---

# Python Backend Development Standards

This document defines coding standards and architectural patterns for Python
backend development, with distinct approaches for brownfield and greenfield
projects.

## General

### Code Structure and Style

- Methods **MUST** be focused on a single responsibility.
- Complex logic **SHOULD** be broken down into smaller, testable units.
- Duplicated blocks of code **MUST** be removed by consolidating logic into
  shared methods.
- Nested logic **SHOULD** be simplified, and deep `if` trees **SHOULD** be
  avoided to reduce cognitive complexity.
- String literals **SHOULD** be replaced with constants or enums to prevent
  duplication.
- Magic numbers **MUST** be replaced with named constants for better
  readability.
- Exceptions **MUST** always be handled meaningfully; `catch` blocks **MUST
  NOT** be left empty.
- Code **MUST** follow the PEP-8 standard, but lines can be up to 120 characters
  long
- Code **MUST** be typed following the abilities of the current python version.

### Dependency Management

- Define versions with fixed version numbers
- If the dependency manager in use supports lock files, they **MUST** be used to
  ensure predictable builds and to mitigate any security risks.

### Testing

- Unit tests **MUST** be written for all public methods.
- Private methods **MUST NOT** be called directly in tests unless specifically
  requested.

### Security

- Parameterized queries **MUST** be used to prevent SQL injection.
- Sensitive data **MUST** be stored securely, and passwords or tokens **MUST
  NOT** be logged.

### General

- Resources **MUST** be managed using context managers (`with`/`async with`) to
  ensure they are closed reliably.
- **Async Programming**: You **MUST** use `async`/`await` for asynchronous
  operations and **MUST NOT** use blocking synchronous calls in async contexts
- **No Raw Dictionaries**: You **MUST NOT** use raw Python dictionaries for
  structured data that requires validation. TypedDict can be used instead.

## Brownfield Projects

### Legacy Code Integration

- Existing code **SHOULD** be updated to use modern syntax where applicable.

### Testing Strategy

#### Pytest

- Use pytest fixtures to avoid duplication of testing data
- Use pytest.mark.parametrize to avoid repeating similar tests with slightly
  different test values.
- Separate sections in the test between setup/execute/assert to delineate which
  parts of the test are for setup code, which is the code being tested in
  execute and what code is doing asserting.

## Greenfield Projects

### Architecture: Hexagonal Design Pattern

You **MUST** implement the **Hexagonal Architecture** (Ports and Adapters)
pattern to maintain clear separation of concerns:

- **Application/Domain Layer**: Contains pure business logic, domain models,
  entities, and business rules (located in `application/domain/`)
- **Application/Services Layer**: Contains use cases, application services, and
  domain logic implementation (located in `application/services/`)
- **Ports Layer**: Defines communication interfaces between the application core
  and external systems (located in `application/ports/`)
- **Adapters Layer**: Contains framework-specific implementations of ports,
  including input adapters (controllers, CLI) and output adapters (databases,
  external APIs, message brokers) (located in `adapters/`)

Business domain logic **MUST** be completely isolated from vendor-specific and
technology-specific code. Domain entities **MUST NOT** have dependencies on
external frameworks or libraries.

### Language Requirements

- **Minimum Python Version**: 3.13+
- **Modern Language Features**: You **SHOULD** use the latest available Python
  language features and best practices
- **Type Hints**: All functions, methods, classes, and variables **MUST** have
  proper type annotations

### Schema-Driven Development

- **Schema First**: You **MUST** define OpenAPI schema before implementation
- **Validation**: All data structures **MUST** be validated against their
  schemas

### Data Validation and Models

- **Pydantic Models**: You **MUST** use Pydantic models for input validation,
  data serialization, and API contracts
- **No Raw Dictionaries**: You **MUST NOT** use raw Python dictionaries for
  structured data that requires validation
- **Strict Typing**: You **SHOULD** enable Pydantic's strict mode for type
  validation
- **Field Validation**: You **SHOULD** use Pydantic validators for complex
  business rule validation

Example:

```python
from pydantic import BaseModel, Field, field_validator
from typing import Annotated
from datetime import datetime

class UserCreateRequest(BaseModel):
    name: Annotated[str, Field(min_length=1, max_length=100)]
    email: Annotated[str, Field(pattern=r'^[^@]+@[^@]+\.[^@]+$')]
    age: Annotated[int, Field(ge=0, le=150)]

    @field_validator('email')
    @classmethod
    def validate_email_domain(cls, v):
        # Custom business validation logic
        return v
```

### Error Handling

- **HTTP Error Models**: You **MUST** throw appropriate exceptions using the
  provided HTTP Error models
- **No Custom Response Logic**: You **MUST NOT** implement custom error response
  serialization or HTTP response handling
- **Centralized Middleware**: All errors **SHALL** be caught and handled by
  centralized error middleware defined in the provided core library
- **Appropriate Status Codes**: You **MUST** use semantically correct HTTP
  status codes through the provided error models

### Project Structure

Code **MUST** be organized following hexagonal architecture:

```
myservice/              # Root App Package
├── adapters/           # Framework specific implementation of ports
│   ├── input/          # App access ports, such as WebMVC Controllers
│   └── output/         # App external dependency ports, such as a WebClient
├── application/        # Portable business domain logic
│   ├── domain/         # Business Domain Models (Entities, DTOs, DAOs)
│   ├── ports/          # Communication Interfaces
│   │   ├── input/      # Input (domain access) Interfaces
│   │   └── output/     # Output (domain dependencies) Interfaces
│   └── services/       # Domain Logic Implementation
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spallempati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

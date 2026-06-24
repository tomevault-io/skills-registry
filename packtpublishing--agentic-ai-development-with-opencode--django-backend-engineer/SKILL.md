---
name: django-backend-engineer
description: Django backend development with DRF, service layer architecture, PostgreSQL, and production-ready patterns. Use for building APIs, models, views, serializers, and tests in Django projects. Use when this capability is needed.
metadata:
  author: PacktPublishing
---

# Django Project Skill Definition

## Purpose

This project is a Django-based web application. All code must be clean, secure, scalable, and consistent with the established patterns in this codebase. When generating or modifying code, always follow the conventions defined below rather than defaulting to generic Django patterns.

---

## Tech Stack

- Python 3.12+
- Django 5.x (latest stable)
- Django REST Framework 3.15+
- PostgreSQL 16+
- Redis (caching and task queue broker, if applicable)
- Celery (background tasks, if applicable)
- Docker and Docker Compose for local development
- python-decouple for environment variable management
- pytest and pytest-django for testing
- factory_boy for test fixtures

---

## Project Structure

```
project_root/
    manage.py
    config/
        __init__.py
        settings/
            __init__.py
            base.py          # Shared settings
            development.py   # Local dev overrides
            production.py    # Production overrides
            test.py          # Test-specific settings
        urls.py              # Root URL conf
        wsgi.py
        asgi.py
    apps/
        <app_name>/
            __init__.py
            models.py
            views.py
            serializers.py
            services.py      # Business logic lives here
            selectors.py     # Complex query logic lives here
            urls.py
            permissions.py
            signals.py
            admin.py
            tests/
                __init__.py
                test_models.py
                test_views.py
                test_services.py
            migrations/
    common/
        models.py            # Abstract base models (TimeStampedModel, etc.)
        permissions.py       # Shared custom permissions
        pagination.py        # Shared pagination classes
        exceptions.py        # Custom exception classes and handler
        utils.py             # Small shared utilities
    requirements/
        base.txt
        development.txt
        production.txt
```

Rules:
- Each feature or domain gets its own Django app inside `apps/`.
- Never place business logic in views or models. Views handle HTTP concerns only. Models handle data integrity only. Business logic goes in `services.py`.
- Complex querysets and data retrieval go in `selectors.py`.
- All apps must be registered using their full path: `apps.<app_name>`.

---

## Coding Standards

- Follow PEP 8 strictly. Line length limit is 88 characters (Black formatter default).
- Use type hints on all function signatures.
- Add docstrings to every class, function, and method. Use Google-style docstring format.
- Use class-based views by default. Use function-based views only for trivially simple endpoints.
- Prefer `get_object_or_404` over manual try/except for object retrieval in views.
- Use f-strings for string formatting. Never use `%` or `.format()`.
- Import ordering: stdlib, third-party, Django, project-local. Use isort to enforce.
- No wildcard imports. No unused imports.
- No print statements in committed code. Use `logging` module instead.

---

## Model Conventions

All models should inherit from a shared abstract base:

```python
# common/models.py
import uuid
from django.db import models

class TimeStampedModel(models.Model):
    id = models.UUIDField(primary_key=True, default=uuid.uuid4, editable=False)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    class Meta:
        abstract = True
        ordering = ["-created_at"]
```

Rules:
- Use UUIDs as primary keys.
- Always define `__str__` on every model.
- Always define `Meta.ordering` explicitly.
- Use `related_name` on all ForeignKey and ManyToMany fields.
- Add `db_index=True` on fields that are frequently filtered or searched.
- Keep model methods limited to data integrity and representation. No HTTP logic, no side effects.

---

## Service Layer Pattern

All business logic lives in service functions. Views call services, never the other way around.

```python
# apps/orders/services.py
from django.db import transaction
from apps.orders.models import Order

def create_order(*, user, items: list[dict]) -> Order:
    """
    Creates a new order for the given user.

    Args:
        user: The user placing the order.
        items: List of dicts with 'product_id' and 'quantity'.

    Returns:
        The created Order instance.

    Raises:
        ValidationError: If items list is empty or a product is unavailable.
    """
    with transaction.atomic():
        order = Order.objects.create(user=user)
        # ... build order items
        return order
```

Rules:
- Service functions use keyword-only arguments (the `*` in the signature) to enforce clarity at call sites.
- Wrap multi-step writes in `transaction.atomic()`.
- Services raise Django `ValidationError` or custom exceptions from `common/exceptions.py` on failure. They never return error dicts or status codes.
- Services never access `request` objects directly. Views extract what is needed and pass it in.

---

## Serializer Conventions

```python
# apps/orders/serializers.py
from rest_framework import serializers
from apps.orders.models import Order

class OrderOutputSerializer(serializers.ModelSerializer):
    class Meta:
        model = Order
        fields = ["id", "user", "status", "created_at"]

class OrderCreateInputSerializer(serializers.Serializer):
    items = serializers.ListField(child=serializers.DictField(), min_length=1)
```

Rules:
- Separate input serializers from output serializers. Name them `<Entity>CreateInputSerializer`, `<Entity>UpdateInputSerializer`, `<Entity>OutputSerializer`.
- Input serializers handle validation only. They do not call `.save()` or `.create()`. The view passes validated data to a service function.
- Output serializers handle representation only.
- Never use `fields = "__all__"`. Always list fields explicitly.

---

## View Conventions

```python
# apps/orders/views.py
from rest_framework import status
from rest_framework.response import Response
from rest_framework.views import APIView
from rest_framework.permissions import IsAuthenticated

from apps.orders.serializers import OrderCreateInputSerializer, OrderOutputSerializer
from apps.orders.services import create_order

class OrderCreateView(APIView):
    permission_classes = [IsAuthenticated]

    def post(self, request):
        serializer = OrderCreateInputSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)

        order = create_order(user=request.user, **serializer.validated_data)

        output = OrderOutputSerializer(order)
        return Response(output.data, status=status.HTTP_201_CREATED)
```

Rules:
- Views are thin. They handle: permissions, input deserialization, calling a service, output serialization, returning a response. Nothing else.
- Always set `permission_classes` explicitly on every view. Never rely on the global default silently.
- Use DRF's `Response`, not Django's `JsonResponse`.
- Return appropriate HTTP status codes. Use `status` constants, not raw integers.

---

## URL Conventions

```python
# apps/orders/urls.py
from django.urls import path
from apps.orders.views import OrderCreateView, OrderDetailView

app_name = "orders"

urlpatterns = [
    path("", OrderCreateView.as_view(), name="create"),
    path("<uuid:pk>/", OrderDetailView.as_view(), name="detail"),
]
```

Rules:
- Every app defines its own `urls.py` with an `app_name`.
- Root `config/urls.py` includes app URLs under a versioned API prefix: `api/v1/`.
- Use `uuid` path converter for primary keys.
- Use trailing slashes consistently.

---

## API Response Format

All API responses follow this envelope structure:

```json
{
    "data": { ... },
    "meta": {
        "page": 1,
        "page_size": 20,
        "total_count": 100
    }
}
```

For errors:

```json
{
    "error": {
        "code": "validation_error",
        "message": "Items list cannot be empty.",
        "details": { ... }
    }
}
```

Implement this via a custom exception handler in `common/exceptions.py` and a response wrapper utility.

---

## Error Handling

- Define custom exception classes in `common/exceptions.py` for domain-specific errors.
- Register a custom DRF exception handler in settings that formats all errors into the standard envelope above.
- Never catch bare `Exception` unless re-raising or logging. Catch specific exceptions.
- Use `logging.exception()` for unexpected errors. Use `logging.warning()` for expected but notable failures.
- Never expose stack traces or internal details in API responses.

---

## Authentication and Permissions

- Use token-based authentication (DRF TokenAuthentication or SimpleJWT, specify which one your project uses).
- Define custom permission classes in `apps/<app_name>/permissions.py` or `common/permissions.py`.
- Every view must have explicit `permission_classes`. No endpoint should be accidentally public.
- Use Django's built-in `User` model or a custom user model inheriting `AbstractUser` (specify which one your project uses).

---

## Security Rules

These are non-negotiable:
- Never hardcode secrets, keys, tokens, or passwords. All secrets come from environment variables via python-decouple.
- Ensure `DEBUG = False` in production settings.
- Validate and sanitize all user input. Never trust client data.
- Keep CSRF middleware active. Keep SecurityMiddleware active.
- Use `ALLOWED_HOSTS` and `CORS_ALLOWED_ORIGINS` explicitly. Never use wildcards in production.
- Use Django ORM for all database queries. Never write raw SQL unless there is a documented performance justification, and always use parameterized queries.
- Set `SECURE_SSL_REDIRECT`, `SESSION_COOKIE_SECURE`, and `CSRF_COOKIE_SECURE` to `True` in production.
- Run `python manage.py check --deploy` before any production deployment.

---

## Database and Migrations

- Always create migrations after model changes: `python manage.py makemigrations`.
- Review generated migration files before committing. Never commit auto-generated migrations blindly.
- Use `RunPython` migrations sparingly and only for data migrations. Always include a reverse function.
- Never edit or delete existing migrations that have been applied to shared environments.
- Use `db_index=True`, `unique=True`, and database constraints where appropriate. Enforce data integrity at the database level, not just application level.
- Avoid N+1 queries. Use `select_related` and `prefetch_related` in selectors.

---

## Testing

- Test runner: `pytest` with `pytest-django`.
- Use `factory_boy` for generating test data. Never create test objects with raw `Model.objects.create()` unless trivially simple.
- Test file structure mirrors the module it tests: `tests/test_models.py`, `tests/test_views.py`, `tests/test_services.py`.
- Every service function must have tests covering the success path and at least one failure/edge case.
- Every API endpoint must have tests covering: correct status codes, response shape, authentication enforcement, and permission enforcement.
- Use `APITestCase` for endpoint tests. Use plain `TestCase` or `pytest` functions for unit tests on services and selectors.
- Aim for meaningful coverage over percentage targets. Do not write trivial tests just to inflate coverage numbers.

---

## Logging

- Use Python's `logging` module. Configure it in `config/settings/base.py`.
- Use named loggers per module: `logger = logging.getLogger(__name__)`.
- Log at appropriate levels: `DEBUG` for development detail, `INFO` for normal operations, `WARNING` for recoverable issues, `ERROR` for failures.
- Never log sensitive data (passwords, tokens, PII).

---

## Dependency Management

- Use `requirements/` directory with split files: `base.txt`, `development.txt`, `production.txt`.
- `development.txt` and `production.txt` both start with `-r base.txt`.
- Pin all dependencies to exact versions.
- Do not add dependencies without a clear justification. Prefer Django's built-in tools and stdlib over third-party packages when the built-in solution is adequate.

---

## AI Behavior Rules

- Do not invent or hallucinate Django APIs, settings, or DRF features. If unsure whether something exists, say so.
- Use only the patterns and conventions defined in this file.
- When creating a new app, follow the exact directory structure shown above.
- When modifying existing code, read the surrounding code first and match its style.
- Ask for clarification when requirements are ambiguous rather than guessing.
- Prefer simple, maintainable solutions over clever abstractions.
- Never add a dependency, middleware, or signal without stating why.
- When suggesting a change, explain what it does and why it is necessary.

---

## Things to Avoid

- Over-engineering. Do not add abstractions, mixins, or patterns until they solve a real repeated problem.
- Adding unnecessary third-party packages.
- Writing long functions. If a function exceeds 30 lines, consider splitting it.
- Mixing frontend concerns into backend code.
- Using `signals` for business logic. Signals are for decoupled side effects only (cache invalidation, audit logging). If the caller should know about the side effect, call it explicitly in a service.
- Using `GenericViewSet` or router magic when explicit `APIView` classes are clearer.
- Returning inconsistent response structures across endpoints.

---
> Source: [PacktPublishing/Agentic-AI-Development-with-OpenCode](https://github.com/PacktPublishing/Agentic-AI-Development-with-OpenCode) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->

---
name: django-expert
description: > Use when this capability is needed.
metadata:
  author: naKarthikSurya
---

# Django Expert Skill

## Goal

Implement production-grade Django/DRF backend features with clean model design, serializer
validation, ViewSet-based APIs, and comprehensive test coverage.

## When to Use

- The project uses Django or Django REST Framework.
- A new model, serializer, viewset, permission, or signal must be implemented.
- An existing Django feature has a bug or architecture violation.

## Project Structure Convention

```
project/
  apps/
    <feature>/
      models.py
      serializers.py
      views.py          # ViewSets or APIViews
      urls.py
      permissions.py
      signals.py
      tasks.py          # Celery tasks
      tests/
        test_models.py
        test_serializers.py
        test_views.py
  config/
    settings/
      base.py
      development.py
      production.py
    urls.py
```

## Implementation Rules

### Models
- Use `verbose_name` and `verbose_name_plural` on every model.
- `created_at = models.DateTimeField(auto_now_add=True)`.
- `updated_at = models.DateTimeField(auto_now=True)`.
- Soft delete via `is_deleted` + custom Manager.
- Every ForeignKey has `on_delete` explicitly set.

### Serializers
- Use `ModelSerializer` for CRUD. Override `validate_<field>()` for field-level validation.
- Use `SerializerMethodField` for computed values.
- Separate serializers for list (minimal) and detail (full) representations.
- Use `read_only_fields = (...)` to prevent mass assignment.

### ViewSets & Permissions
- Use `ModelViewSet` for full CRUD. Use `GenericAPIView` mixins for partial sets.
- Apply permissions via `permission_classes` on the ViewSet or per-action via `get_permissions()`.
- Apply filtering via `django-filter` or custom `get_queryset()`.
- Always use `select_related` and `prefetch_related` to avoid N+1 queries.

### Settings
- Separate settings files for development, testing, production.
- All secrets via environment variables with `python-decouple` or `django-environ`.
- `DEBUG = False` enforced in production settings.

### Database
- All schema changes via Django migrations. Never modify DB directly.
- Use `database_transactions` in tests.

## Review Checklist

- [ ] Models have `created_at`, `updated_at`, and `__str__` method
- [ ] Serializers have validation for all required fields
- [ ] ViewSets use `select_related`/`prefetch_related` to prevent N+1
- [ ] Permissions applied at viewset level
- [ ] Tests cover model, serializer, and viewset layers

---
> Source: [naKarthikSurya/Talos](https://github.com/naKarthikSurya/Talos) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->

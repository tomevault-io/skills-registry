---
name: django-fullstack-dev
description: Comprehensive coding guidelines for Django Backend and Frontend development Use when this capability is needed.
metadata:
  author: ozansaridogan
---

# Project Context
This is a full-stack web application built with Django. The AI assistant should act as a Senior Django Developer, prioritizing security, performance, and maintainability.

## 1. Backend Guidelines (Python & Django)

### General Architecture
- **Fat Models, Thin Views:** Keep business logic out of views. Encapsulate logic in Model methods or separate `services.py` / `selectors.py` files.
- **Formatting:** Follow PEP 8 strict guidelines.
- **Type Hinting:** Use Python type hints for all function arguments and return values.

### Django ORM & Database
- **Performance:** Always use `select_related` (for ForeignKey) and `prefetch_related` (for M2M) to prevent N+1 query problems.
- **Queries:** Avoid raw SQL. Use Django's query expressions (F, Q) and aggregation functions.
- **Models:** Always define `__str__` methods for models. Use explicit `related_name` in relationships.

### Views & APIs
- **Structure:** Use Class-Based Views (CBVs) for standard CRUD operations to reduce boilerplate. Use Function-Based Views (FBVs) only for highly custom logic.
- **Security:**
  - Never disable CSRF protection globally.
  - Validate all user inputs using Django Forms or Serializers (if using DRF).
  - Follow OWASP guidelines (prevent SQL Injection, XSS).

## 2. Frontend Guidelines (Templates & Static Files)

### Django Templates (DTL)
- **Logic:** Keep templates logic-free. Do not perform complex calculations or database queries inside templates. Pass pre-calculated data from the View context.
- **Security:** Rely on Django's auto-escaping. Use `|safe` filter only when absolutely necessary and verified.

### HTML, CSS, & JavaScript
- **Structure:** Use Semantic HTML5 tags (`<header>`, `<main>`, `<article>`, `<footer>`).
- **Static Files:** All CSS and JS must reside in the `static/` directory, never inline within HTML files.
- **JavaScript:** Use modern ES6+ syntax (Arrow functions, const/let). Ensure code is DOM-ready before execution.
- **Styling:** Follow a mobile-first approach.

## 3. Documentation & Testing
- **Docstrings:** specific classes and complex functions must have a docstring explaining arguments and return values.
- **Testing:** Write unit tests for Models and integration tests for Views using `pytest` or Django's `TestCase`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ozansaridogan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

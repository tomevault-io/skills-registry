---
name: flask-testing-patterns
description: Rigorous testing of Flask applications using Pytest and professional mocking strategies. Use when this capability is needed.
metadata:
  author: jcorpac
---

# Flask Testing Patterns

Untested code is broken code. This skill focuses on production-grade testing workflows.

## The Test Client
Use `app.test_client()` to simulate requests without running a live server.

## Fixtures (Pytest)
- **app**: A fixture that yields a pre-configured application instance.
- **db**: A fixture that sets up an in-memory database and cleans it after each test.
- **client**: A pre-authenticated client for integration testing.

## Strategic Layers
- **Unit Tests**: Testing individual models, forms, and business logic.
- **Integration Tests**: Testing the request/response cycle through views and database persistence.

## Best Practices
- **Isolated Databases**: Always use an in-memory database (e.g., SQLite `:memory:`) for tests to ensure speed and isolation.
- **Coverage**: Use `pytest-cov` to identify untested execution paths.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

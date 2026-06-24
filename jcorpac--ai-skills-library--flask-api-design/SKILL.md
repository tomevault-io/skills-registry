---
name: flask-api-design
description: Building clean, RESTful APIs with Marshmallow for serialization and OpenAPI documentation. Use when this capability is needed.
metadata:
  author: jcorpac
---

# Flask API Design

APIs should be self-documenting, consistent, and easy to consume.

## Serialization (Marshmallow)
- **Schemas**: Transforming complex objects to/from primitive Python types.
- **Validation**: Enforcing data constraints at the API layer.

## RESTful Routing
- **Nouns not Verbs**: `/users/1` instead of `/getUser/1`.
- **HTTP Methods**: GET (read), POST (create), PUT/PATCH (update), DELETE (remove).

## Documentation
Use **Flask-Smorest** or **Flasgger** to automatically generate Swagger/OpenAPI UI for your endpoints.

## Best Practices
- **Status Codes**: Return appropriate codes (e.g., `201 Created`, `404 Not Found`, `422 Unprocessable Entity`).
- **Versioning**: Prefix routes with `/api/v1/`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

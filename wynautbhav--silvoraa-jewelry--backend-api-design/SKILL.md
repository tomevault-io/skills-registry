---
name: backend-api-design
description: Guidelines for designing clean, scalable, and secure RESTful APIs. Use when defining API endpoints or request/response structures. Use when this capability is needed.
metadata:
  author: wynautbhav
---

# Backend API Design Skill

## Naming & Methods
- **GET** `/resources` (List), `/resources/:id` (Get one)
- **POST** `/resources` (Create)
- **PUT/PATCH** `/resources/:id` (Update)
- **DELETE** `/resources/:id` (Delete)
- Use **Nouns** (plural), not verbs. (e.g., `users`, not `getUsers`).

## Responses
- Always return standard status codes (200, 201, 400, 401, 403, 404, 500).
- Standard JSON envelope:
```json
{
  "success": true,
  "data": { ... },
  "error": null
}

```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wynautbhav) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

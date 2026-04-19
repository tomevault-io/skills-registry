---
name: fastapi-secure-logic-skill
description: Use when creating REST endpoints or backend logic to enforce user isolation and JWT verification.
metadata:
  author: s-952205
---

# FastAPI Secure Logic Skill

## Instructions
1. **JWT Verification**: Create a FastAPI dependency to decode and validate JWTs using the shared secret.
2. **Data Filtering**: Always add a `user_id` filter to every SQLModel query to prevent cross-user data access.
3. **Input Validation**: Use Pydantic models to strictly validate incoming JSON payloads.
4. **Standard Errors**: Raise `HTTPException(401)` for invalid tokens and `404` for missing resources.

## Examples
- **Secure Route**: `@app.get("/tasks") def read_tasks(user: User = Depends(get_current_user)):`
- **Query**: `select(Task).where(Task.user_id == user.id)`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/s-952205) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

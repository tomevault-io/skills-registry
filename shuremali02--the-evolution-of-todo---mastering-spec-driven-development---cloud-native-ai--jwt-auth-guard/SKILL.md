---
name: jwt-auth-guard
description: name: "jwt-auth-guard" Use when this capability is needed.
metadata:
  author: shuremali02
---
---
name: "jwt-auth-guard"
description: "Verifies Better Auth JWT tokens inside FastAPI and extracts authenticated user identity for secure API access."
version: "1.0.0"
---

# JWT Auth Guard Skill

## When to Use This Skill

- Connecting Better Auth with FastAPI
- Securing REST endpoints
- Enforcing user isolation

## How This Skill Works

1. Reads JWT from Authorization header
2. Verifies using BETTER_AUTH_SECRET
3. Extracts user_id & email
4. Injects user into request
5. Blocks invalid tokens

## Output Format

- Middleware
- Dependency
- User object available in routes

## Example

**Input:** "Secure all API routes"

**Output:**  
FastAPI middleware enforcing JWT auth on all `/api/*` endpoints
 
 

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shuremali02) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

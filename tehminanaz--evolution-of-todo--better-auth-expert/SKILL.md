---
name: better-auth-expert
description: Specialized persona for implementing secure, frictionless authentication that bridges Next.js 16 (Client) and FastAPI (Server). Use when this capability is needed.
metadata:
  author: tehminanaz
---

# Better Auth Expert

## Overview

This skill provides a specialized persona for implementing secure, frictionless authentication that bridges Next.js 16 (Client) and FastAPI (Server). It focuses on using shared secrets, stateless verification, and strict typing across the frontend-backend boundary.

---

# Process

## 🚀 High-Level Workflow

### Phase 1: Conceptual Understanding

#### 1.1 The Bridge
Using a shared `BETTER_AUTH_SECRET` to mint tokens on the frontend and verify them on the backend. This eliminates the need for complex session synchronization.

#### 1.2 Stateless Verification
The backend does not need to query the frontend's session database for every request; it trusts the cryptographic signature of the JWT.

#### 1.3 Type Safety
Ensuring user context is correctly typed across the TypeScript/Python boundary, minimizing runtime errors.

---

## 🛠 Implementation Best Practices

### Phase 2: Configuration & Security

#### 2.1 Backend Security
-   **JWT Plugin**: Enabled for cross-service compatibility.
-   **Secure Headers**: Using standard `Authorization: Bearer` patterns.
-   **Dependency Injection**: Verifying users in FastAPI dependencies.

#### 2.2 Frontend Integration
-   **Better Auth Client**: Correctly configured for Next.js App Router.
-   **Zero-Trust**: Always assume inputs are malicious until verified.

---

# Reference Files

## 📚 Documentation
-   **Better Auth Docs**: Refer to official documentation for configuration details.
-   **JWT Libraries**: `python-jose` or similar for Python verification.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tehminanaz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

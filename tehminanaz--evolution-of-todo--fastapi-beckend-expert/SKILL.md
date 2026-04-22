---
name: fastapi-backend-expert
description: Comprehensive persona for building high-performance, secure, and type-safe backend services with FastAPI, SQLModel, and Neon. Use when this capability is needed.
metadata:
  author: tehminanaz
---

# FastAPI Backend Expert

## Overview

This skill provides a comprehensive persona and set of guidelines for building high-performance, secure, and type-safe backend services. It synthesizes best practices from top experts in the field.

---

# Process

## 🚀 High-Level Workflow

### Phase 1: Core Principles

#### 1.1 FastAPI & SQLModel (Sebastián Ramírez)
-   **Type Hints**: Use standard Python type hints everywhere.
-   **Pydantic**: Robust data validation and serialization.
-   **Async/Await**: High-concurrency I/O operations.

#### 1.2 Neon Serverless (Nikita Shamgunov)
-   **Serverless Architecture**: Scaling to zero.
-   **Connection Pooling**: Managing database connections efficiently.

#### 1.3 Security (Troy Hunt)
-   **Defense in Depth**: Layered security measures.
-   **Input Sanitization**: Never trust client input.
-   **JWT Verification**: Stateless auth.

---

## 🛠 Implementation Details

### Phase 2: Components & Best Practices

#### 2.1 Dependency Injection
Use FastAPI's dependency injection system for database sessions (`SessionDep`) and current user verification (`CurrentUserDep`).

#### 2.2 Database Modeling
-   Use **SQLModel** for defining both database tables and Pydantic models.
-   Define clear **Relationships** between entities.
-   Use **Foreign Key Indexes** for performance.

#### 2.3 Security Measures
-   **Security Headers**: configure CORS and other headers.
-   **Strict Validation**: Pydantic models for all request bodies.

---

## Phase IV Lessons Learned (Critical)

### 1. Gunicorn Dependency for Production FastAPI Images

**Problem**: Container crashed with `exec: "gunicorn": executable file not found in $PATH`

**Root Cause**: Dockerfile used Gunicorn as the CMD but `gunicorn` wasn't in `pyproject.toml` dependencies.

**Solution**:
```toml
# In pyproject.toml - ADD gunicorn explicitly
[project]
dependencies = [
    "fastapi>=0.115.0",
    "uvicorn[standard]>=0.32.0",
    "gunicorn>=21.2.0",  # ✅ Required for production WSGI deployment
    # ... other dependencies
]
```

**Why Gunicorn?**:
- Better process management than raw uvicorn
- Handles worker spawning and health monitoring
- Standard for Python production deployments

**Prevention**: Audit all CMD/ENTRYPOINT executables against dependencies list.

---

### 2. asyncpg for Neon PostgreSQL in Containerized Environments

**Problem**: Database connection failures in Kubernetes pods when using `psycopg2` with Neon serverless endpoints.

**Root Cause**: Neon requires async driver `asyncpg` for proper connection pooling with serverless functions.

**Solution**:
```python
# In config.py - Use asyncpg URL format
from pydantic import Field
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    database_url: str = Field(
        ..., description="Database connection string"
    )

    @validator("database_url", pre=True)
    def use_async_driver(cls, v):
        if isinstance(v, str) and v.startswith("postgresql://"):
            # Convert to asyncpg format for Neon
            return v.replace("postgresql://", "postgresql+asyncpg://", 1)
        return v

settings = Settings()
```

```yaml
# In deployment, use the async URL
env:
- name: DATABASE_URL
  valueFrom:
    secretKeyRef:
      name: backend-secrets
      key: DATABASE_URL  # Should be: postgresql+asyncpg://...
```

**Why asyncpg?**:
- Native async support for Python's asyncio
- Better connection pooling for serverless/containers
- Required by Neon's serverless endpoints

**Prevention**: Test database connectivity in container before deployment.

---

### 3. /ready Probe Implementation for Database Verification

**Problem**: Pods appeared "ready" but failed immediately when receiving traffic due to database disconnect.

**Root Cause**: Only `/health` endpoint existed; no readiness check that verified database connectivity.

**Solution**:
```python
# In main.py - Implement /ready with DB verification
from fastapi import FastAPI, HTTPException
from sqlmodel import text
from .db import engine

@app.get("/ready")
async def readiness_check():
    """
    Readiness check - verifies app is ready to serve traffic.
    Includes database connectivity verification for Kubernetes readiness probes.
    """
    try:
        # Check database connectivity
        with engine.connect() as conn:
            result = conn.execute(text("SELECT 1"))
            result.fetchone()

        return {
            "status": "ready",
            "database": "connected",
            "message": "Application is ready to serve traffic"
        }
    except Exception as e:
        raise HTTPException(
            status_code=503,
            detail={
                "status": "not_ready",
                "database": "disconnected",
                "message": "Application is not ready - database connection failed"
            }
        )
```

**Kubernetes Deployment**:
```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8000
  initialDelaySeconds: 10
  periodSeconds: 10
  failureThreshold: 3
```

**Why This Matters**:
- Prevents routing traffic to pods that can't serve requests
- Enables Kubernetes to auto-restart unhealthy pods
- Provides clear debugging info when DB connection fails

**Prevention**: Always implement `/ready` that verifies all external dependencies.

---

### 4. Silent Crashes from Missing Environment Variables

**Problem**: Container crashed silently with Pydantic validation error: `Field required [type=missing]` for `secret_key`.

**Root Cause**: Config expected `SECRET_KEY` environment variable but deployment template didn't include it.

**Solution**:
```python
# In config.py - Provide helpful error messages
class Settings(BaseSettings):
    secret_key: str = Field(..., min_length=32)
    database_url: str = Field(...)

    class Config:
        @classmethod
        def customise_sources(cls, init_settings, env_settings, file_secret_settings):
            # Ensure env vars are checked
            return (init_settings, env_settings, file_secret_settings)

# Required environment variables that MUST be set:
REQUIRED_ENV_VARS = ["SECRET_KEY", "DATABASE_URL", "GROQ_API_KEY"]
```

**Debugging Silent Crashes**:
```bash
# Check pod logs for startup errors
kubectl logs <pod-name> -n <namespace>

# For crashes during startup, check previous container logs
kubectl logs <pod-name> -n <namespace> --previous

# Describe pod for events
kubectl describe pod <pod-name> -n <namespace>
```

**Prevention**:
1. Create `.env.example` with ALL required variables
2. Validate all required env vars at startup
3. Use Kubernetes Secret templates that match config requirements

---

## Reference Files

## 📚 Documentation
-   **FastAPI Docs**: https://fastapi.tiangolo.com
-   **SQLModel Docs**: https://sqlmodel.tiangolo.com
-   **Neon Docs**: https://neon.tech/docs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tehminanaz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

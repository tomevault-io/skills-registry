---
name: local-setup-structure
description: Guidelines for implementing Docker-based local development with LOCAL_RUN environment flag. Creates unified docker-compose.local.yml for all containers. Used by /local-setup command. Use when this capability is needed.
metadata:
  author: wtah
---

# Local Setup Skill

This skill provides guidance for **creating a Docker-based local development environment** using a unified `docker-compose.local.yml` at the project root. All containers run via Docker for reliable process management.

**CRITICAL**: The primary output is `docker-compose.local.yml` which orchestrates ALL containers (infrastructure + application) together.

## Docker-Based Approach

```
/local-setup command
        ↓
Creates docker-compose.local.yml at project root
        ↓
All containers run via: docker-compose -f docker-compose.local.yml up -d
        ↓
Stop via: docker-compose -f docker-compose.local.yml down
```

### Benefits

| Benefit | Explanation |
|---------|-------------|
| **Reliable cleanup** | `docker-compose down` always works |
| **Consistent environments** | Same behavior on any machine |
| **No process management** | No PID tracking, port killing, etc. |
| **Built-in health checks** | Docker handles service readiness |
| **Easy debugging** | `docker-compose logs` shows all output |

---

## Core Concept

```
LOCAL_RUN=true  → Bypass auth + Use local service adapters
LOCAL_RUN=false → Normal production auth + Cloud adapters (unchanged)
LOCAL_RUN unset → Same as false (default to production)
```

The `LOCAL_RUN` flag controls TWO aspects:
1. **Authentication**: Bypass auth, inject test user
2. **Service Adapters**: Use local Docker-based services instead of cloud

---

## Related Skills

- **`local-alternatives`**: Reference guide for selecting local alternatives to cloud services
- **Architects must load `local-alternatives`** when designing adapter patterns

---

## Unified docker-compose.local.yml Structure

The `/local-setup` command creates a single `docker-compose.local.yml` at the project root:

```yaml
# docker-compose.local.yml - at project root
version: '3.8'

services:
  # Infrastructure Services
  mongodb:
    image: mongo:7
    container_name: local-mongodb
    ports: ["27017:27017"]
    healthcheck:
      test: ["CMD", "mongosh", "--eval", "db.adminCommand('ping')"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    container_name: local-redis
    ports: ["6379:6379"]
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]

  azurite:
    image: mcr.microsoft.com/azure-storage/azurite
    container_name: local-azurite
    ports: ["10000:10000", "10001:10001", "10002:10002"]

  mailhog:
    image: mailhog/mailhog
    container_name: local-mailhog
    ports: ["1025:1025", "8025:8025"]

  # Application Containers (built from Dockerfile.local)
  api:
    build:
      context: ./api
      dockerfile: Dockerfile.local
    container_name: local-api
    ports: ["3000:3000"]
    environment:
      - LOCAL_RUN=true
      - MONGODB_URI=mongodb://mongodb:27017
      - REDIS_URL=redis://redis:6379
    depends_on:
      mongodb:
        condition: service_healthy
      redis:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "wget", "--spider", "-q", "http://localhost:3000/health"]

  # ... more application containers

networks:
  local-network:
    driver: bridge

volumes:
  mongodb_data:
  redis_data:
```

### Key Concepts

1. **All services in one file** - Infrastructure AND application containers
2. **Use Docker service names** - `mongodb` not `localhost` in connection strings
3. **Health checks with conditions** - `depends_on` with `condition: service_healthy`
4. **Build from Dockerfile.local** - Each container has its own `Dockerfile.local`

## Adapter Pattern for LOCAL_RUN

When `LOCAL_RUN=true`, containers use Docker service names instead of cloud endpoints:

| Cloud Service | Local Alternative | Connection String |
|---------------|-------------------|-------------------|
| Azure Cosmos DB | MongoDB | `mongodb://mongodb:27017` |
| Azure Redis Cache | Redis | `redis://redis:6379` |
| Azure Blob/Queue | Azurite | `DefaultEndpointsProtocol=http;...BlobEndpoint=http://azurite:10000/...` |
| SendGrid | MailHog | `smtp://mailhog:1025` |

**Note**: Use Docker service names (e.g., `mongodb`, `redis`, `azurite`) NOT `localhost` because containers communicate over the Docker network.

---

## Authentication Bypass (Part 1)

---

## Technology Agnostic

This skill is **technology-agnostic**. All implementation details depend on:

```
.constraints/TECHNOLOGY.md
```

**Always read this file first** to understand:
- Programming language(s)
- Framework(s) used
- Authentication approach
- Environment variable handling

---

## The LOCAL_RUN Pattern

### Entry Point Interception

Add the LOCAL_RUN check at the **very start** of authentication logic:

```
┌─────────────────────────────────────────┐
│           Auth Entry Point              │
├─────────────────────────────────────────┤
│  IF LOCAL_RUN == "true":                │
│      → Inject test user                 │
│      → Return (skip auth)               │
│                                         │
│  // Existing auth code below            │
│  // COMPLETELY UNCHANGED                │
│  ...                                    │
└─────────────────────────────────────────┘
```

### Why Entry Point?

- **Earliest interception**: Bypass happens before any auth logic
- **Minimal changes**: Only add lines at the top
- **Clear separation**: New code vs existing code is obvious
- **Easy to remove**: If LOCAL_RUN is removed later, just delete the if-block

---

## Implementation Patterns

### Backend: Auth Middleware

#### Python (FastAPI)

```python
# auth/dependencies.py
import os
from typing import Annotated
from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer

from .fixtures.local_test_user import LOCAL_TEST_USER

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

async def get_current_user(
    token: Annotated[str, Depends(oauth2_scheme)]
):
    # ========== LOCAL_RUN BYPASS - START ==========
    if os.getenv("LOCAL_RUN", "").lower() == "true":
        return LOCAL_TEST_USER
    # ========== LOCAL_RUN BYPASS - END ==========

    # Existing auth logic (UNCHANGED)
    credentials_exception = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
    )
    try:
        # ... existing token validation ...
        pass
    except Exception:
        raise credentials_exception
```

#### Python (Flask)

```python
# auth/middleware.py
import os
from functools import wraps
from flask import request, jsonify

from .fixtures.local_test_user import LOCAL_TEST_USER

def require_auth(f):
    @wraps(f)
    def decorated(*args, **kwargs):
        # ========== LOCAL_RUN BYPASS - START ==========
        if os.getenv("LOCAL_RUN", "").lower() == "true":
            request.user = LOCAL_TEST_USER
            return f(*args, **kwargs)
        # ========== LOCAL_RUN BYPASS - END ==========

        # Existing auth logic (UNCHANGED)
        auth_header = request.headers.get("Authorization")
        if not auth_header:
            return jsonify({"error": "Unauthorized"}), 401
        # ... existing validation ...
        return f(*args, **kwargs)
    return decorated
```

#### Node.js (Express)

```javascript
// middleware/auth.js
const { LOCAL_TEST_USER } = require('../fixtures/localTestUser');

function authMiddleware(req, res, next) {
    // ========== LOCAL_RUN BYPASS - START ==========
    if (process.env.LOCAL_RUN === 'true') {
        req.user = LOCAL_TEST_USER;
        return next();
    }
    // ========== LOCAL_RUN BYPASS - END ==========

    // Existing auth logic (UNCHANGED)
    const authHeader = req.headers.authorization;
    if (!authHeader) {
        return res.status(401).json({ error: 'Unauthorized' });
    }
    // ... existing validation ...
    next();
}

module.exports = { authMiddleware };
```

#### TypeScript (NestJS)

```typescript
// guards/auth.guard.ts
import { Injectable, CanActivate, ExecutionContext } from '@nestjs/common';
import { LOCAL_TEST_USER } from '../fixtures/local-test-user';

@Injectable()
export class AuthGuard implements CanActivate {
    canActivate(context: ExecutionContext): boolean {
        // ========== LOCAL_RUN BYPASS - START ==========
        if (process.env.LOCAL_RUN === 'true') {
            const request = context.switchToHttp().getRequest();
            request.user = LOCAL_TEST_USER;
            return true;
        }
        // ========== LOCAL_RUN BYPASS - END ==========

        // Existing auth logic (UNCHANGED)
        const request = context.switchToHttp().getRequest();
        const token = request.headers.authorization;
        if (!token) {
            return false;
        }
        // ... existing validation ...
        return true;
    }
}
```

### Frontend: Auth Provider/Context

#### React (Context)

```tsx
// contexts/AuthContext.tsx
import React, { createContext, useState, useEffect, ReactNode } from 'react';
import { LOCAL_AUTH_STATE } from '../mocks/localAuthState';

interface AuthContextType {
    user: User | null;
    isAuthenticated: boolean;
    login: () => void;
    logout: () => void;
}

export const AuthContext = createContext<AuthContextType | null>(null);

export function AuthProvider({ children }: { children: ReactNode }) {
    // ========== LOCAL_RUN BYPASS - START ==========
    if (import.meta.env.VITE_LOCAL_RUN === 'true') {
        return (
            <AuthContext.Provider value={LOCAL_AUTH_STATE}>
                {children}
            </AuthContext.Provider>
        );
    }
    // ========== LOCAL_RUN BYPASS - END ==========

    // Existing auth logic (UNCHANGED)
    const [user, setUser] = useState<User | null>(null);
    const [isAuthenticated, setIsAuthenticated] = useState(false);

    useEffect(() => {
        // ... existing auth initialization ...
    }, []);

    // ... rest of existing implementation ...
}
```

#### Vue (Composable/Store)

```typescript
// composables/useAuth.ts
import { ref, readonly } from 'vue';
import { LOCAL_AUTH_STATE } from '../mocks/localAuthState';

export function useAuth() {
    // ========== LOCAL_RUN BYPASS - START ==========
    if (import.meta.env.VITE_LOCAL_RUN === 'true') {
        return {
            user: readonly(ref(LOCAL_AUTH_STATE.user)),
            isAuthenticated: readonly(ref(true)),
            login: () => {},
            logout: () => {},
        };
    }
    // ========== LOCAL_RUN BYPASS - END ==========

    // Existing auth logic (UNCHANGED)
    const user = ref(null);
    const isAuthenticated = ref(false);

    // ... rest of existing implementation ...
}
```

#### Angular (Service/Guard)

```typescript
// services/auth.service.ts
import { Injectable } from '@angular/core';
import { environment } from '../environments/environment';
import { LOCAL_AUTH_STATE } from '../mocks/local-auth-state';

@Injectable({ providedIn: 'root' })
export class AuthService {
    private user: User | null = null;
    private isAuthenticated = false;

    constructor() {
        // ========== LOCAL_RUN BYPASS - START ==========
        if (environment.localRun) {
            this.user = LOCAL_AUTH_STATE.user;
            this.isAuthenticated = true;
            return;
        }
        // ========== LOCAL_RUN BYPASS - END ==========

        // Existing auth logic (UNCHANGED)
        // ... existing initialization ...
    }
}
```

---

## Test User Fixture

### Standard Test User

```typescript
// fixtures/local-test-user.ts
export const LOCAL_TEST_USER = {
    id: 'test-user-local-dev',
    email: 'localdev@test.local',
    name: 'Local Developer',
    role: 'admin',
    permissions: ['*'],
    createdAt: '2024-01-01T00:00:00Z',
};
```

```python
# fixtures/local_test_user.py
LOCAL_TEST_USER = {
    "id": "test-user-local-dev",
    "email": "localdev@test.local",
    "name": "Local Developer",
    "role": "admin",
    "permissions": ["*"],
    "created_at": "2024-01-01T00:00:00Z",
}
```

### Mock Auth State (Frontend)

```typescript
// mocks/localAuthState.ts
import { LOCAL_TEST_USER } from '../fixtures/local-test-user';

export const LOCAL_AUTH_STATE = {
    user: LOCAL_TEST_USER,
    isAuthenticated: true,
    login: () => Promise.resolve(),
    logout: () => Promise.resolve(),
    getToken: () => 'local-dev-token',
};
```

---

## Environment Variable Handling

### Backend

```bash
# .env.example
# Local Development
# Set to 'true' to bypass authentication and use test user
LOCAL_RUN=false
```

Check pattern:
```python
# Python
os.getenv("LOCAL_RUN", "").lower() == "true"
```

```javascript
// Node.js
process.env.LOCAL_RUN === 'true'
```

### Frontend

Frontend frameworks require environment variable prefixes:

| Framework | Prefix | Example |
|-----------|--------|---------|
| Vite | `VITE_` | `VITE_LOCAL_RUN` |
| Create React App | `REACT_APP_` | `REACT_APP_LOCAL_RUN` |
| Next.js | `NEXT_PUBLIC_` | `NEXT_PUBLIC_LOCAL_RUN` |
| Vue CLI | `VUE_APP_` | `VUE_APP_LOCAL_RUN` |
| Angular | In `environment.ts` | `environment.localRun` |

```bash
# .env.example (Vite)
VITE_LOCAL_RUN=false
```

```typescript
// Check pattern (Vite)
import.meta.env.VITE_LOCAL_RUN === 'true'
```

---

## Documentation Updates

### README Section

Add to each container's README:

```markdown
## Local Development (No Auth)

To run locally without authentication infrastructure:

1. Copy `.env.example` to `.env`
2. Set `LOCAL_RUN=true` (or `VITE_LOCAL_RUN=true` for frontend)
3. Start the application
4. Access as test user (no login required)

### Test User

When `LOCAL_RUN=true`, you are automatically authenticated as:

| Field | Value |
|-------|-------|
| ID | test-user-local-dev |
| Email | localdev@test.local |
| Name | Local Developer |
| Role | admin |

**Note**: This bypasses all authentication. Only use for local development.
```

---

## Testing Patterns

### Backend Test: LOCAL_RUN=true

```bash
# Expected: 200 OK
LOCAL_RUN=true curl -X GET http://localhost:3000/api/protected
```

### Backend Test: LOCAL_RUN=false

```bash
# Expected: 401 Unauthorized
LOCAL_RUN=false curl -X GET http://localhost:3000/api/protected
```

### Frontend Test

```bash
# Build and run with LOCAL_RUN=true
VITE_LOCAL_RUN=true npm run build
npm run preview

# Expected: Protected routes accessible without login
```

---

## Safety Guarantees

### What MUST NOT Change

| Aspect | Requirement |
|--------|-------------|
| **Token validation** | Real tokens validated when LOCAL_RUN=false |
| **Auth errors** | Proper 401 returned when LOCAL_RUN=false |
| **Login flow** | Normal redirect when LOCAL_RUN=false |
| **Security headers** | No headers removed |
| **Audit logging** | Events still logged |

### Verification Checklist

- [ ] LOCAL_RUN=false: Backend returns 401 for unauthenticated
- [ ] LOCAL_RUN=false: Frontend redirects to login
- [ ] LOCAL_RUN unset: Same as LOCAL_RUN=false
- [ ] Existing tests still pass
- [ ] No production code paths modified

---

## Anti-Patterns to Avoid

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| **Removing auth code** | Breaks production | Add bypass, don't remove |
| **Check after validation** | Bypass doesn't work | Check FIRST |
| **Modifying production paths** | Regression risk | Only add new code at entry |
| **Hardcoded values** | Maintenance burden | Use fixtures/constants |
| **No fallthrough** | Auth breaks | Always continue to existing |
| **Wrong env prefix** | Frontend can't read | Use VITE_, NEXT_PUBLIC_, etc. |
| **Missing documentation** | Discovery problem | Update README |

---

## File Locations

### Where to Add Bypass

| Type | Typical Location |
|------|------------------|
| **FastAPI** | `auth/dependencies.py` or `middleware/auth.py` |
| **Flask** | `auth/middleware.py` or decorators |
| **Express** | `middleware/auth.js` |
| **NestJS** | `guards/auth.guard.ts` |
| **React** | `contexts/AuthContext.tsx` or `providers/Auth.tsx` |
| **Vue** | `composables/useAuth.ts` or `store/auth.ts` |
| **Angular** | `services/auth.service.ts` or `guards/auth.guard.ts` |

### Where to Put Fixtures

| Type | Typical Location |
|------|------------------|
| **Backend** | `fixtures/local_test_user.{ext}` or `test/fixtures/` |
| **Frontend** | `mocks/localAuthState.{ext}` or `__mocks__/` |

---

## Quick Reference

### Implementation Steps

1. Read `.constraints/TECHNOLOGY.md` for tech stack
2. Locate auth entry point (middleware/guard/provider)
3. Add LOCAL_RUN check at the **very start**
4. Create test user fixture
5. Create mock auth state (frontend)
6. Update `.env.example` files
7. Update README documentation

### Testing Steps

1. Test LOCAL_RUN=true: Verify bypass works
2. Test LOCAL_RUN=false: Verify auth required
3. Test LOCAL_RUN unset: Verify defaults to false
4. Run existing tests: Verify no regression

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wtah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

---
name: api-design-security
description: Design and implement secure admin APIs in Next.js 16 with hardened security, RBAC, CSRF protection, tenant isolation, and audit logging. Use when creating new admin API routes, implementing security controls, or ensuring API compliance with corporate security standards. Use when this capability is needed.
metadata:
  author: artisanclarinets
---

# API Design & Security

A comprehensive guide to designing and implementing secure admin APIs in the Vantus Systems Next.js 16 platform, following Fortune-500 security standards with RBAC, CSRF protection, tenant isolation, and comprehensive audit logging.

## Quick Start

Create a secure admin API route in 5 steps:

1. **Choose the right wrapper**: Use `adminRead` for GET operations, `adminMutation` for writes
2. **Define Zod schema**: Validate input with strict type checking
3. **Apply tenant scoping**: Ensure multi-tenant data isolation
4. **Use safe selects**: Prevent sensitive data leakage
5. **Test security**: Verify CSRF, RBAC, and audit logging

```typescript
// Example: Secure user creation API
export async function POST(req: NextRequest) {
  return adminMutation(req, { 
    permissions: ["users.write"],
    audit: { action: "create_user", resource: "user" }
  }, async (user, body) => {
    const validatedData = createUserSchema.parse(body);
    // Implementation with tenant scoping and security
  });
}
```

## Core Concepts

### Security Layers

**Defense in Depth**: Multiple overlapping security controls:

- **Network Level**: IP allowlists, geographic restrictions, time-based access
- **Application Level**: Authentication, authorization, session management
- **API Level**: CSRF protection, input validation, rate limiting
- **Data Level**: Tenant isolation, safe selects, audit logging

### Admin Route Patterns

**Two Implementation Approaches**:

1. **Direct Pattern**: Manual security enforcement
   - `requireAdmin()` for auth/RBAC
   - `assertSameOrigin()` for CSRF layer 1
   - Manual audit logging

2. **Wrapped Pattern**: Automated security
   - `adminRead()` / `adminMutation()` wrappers
   - Automatic CSRF, RBAC, audit, rate limiting

### Key Security Components

- **Proxy.ts**: Centralized request interception with CSP, nonce, authentication
- **Config/Security.ts**: Environment-driven security configuration
- **Lib/Admin/Route.ts**: Hardened API wrappers with uniform enforcement
- **Lib/Admin/Audit.ts**: Comprehensive audit logging with redaction

## API Design Workflows

### Creating a New Admin API Route

**Step 1: Choose Route Location**
```typescript
// Admin APIs go in app/api/admin/
// Follow RESTful naming: resource/[id]/action
app/api/admin/projects/route.ts          // GET/POST /api/admin/projects
app/api/admin/projects/[id]/route.ts     // GET/PUT/DELETE /api/admin/projects/[id]
```

**Step 2: Select Security Pattern**

For simple reads:
```typescript
export async function GET() {
  return adminRead(req, { permissions: ["projects.read"] }, async (user) => {
    // Implementation
  });
}
```

For mutations with full security:
```typescript
export async function POST(req: NextRequest) {
  return adminMutation(req, { 
    permissions: ["projects.write"],
    audit: { action: "create_project", resource: "project" }
  }, async (user, body) => {
    // Implementation
  });
}
```

**Step 3: Implement Business Logic**

```typescript
// Example from app/api/admin/projects/route.ts
const createProjectSchema = z.object({
  name: z.string().min(1, "Name is required"),
  description: z.string().optional(),
  tenantId: z.string().optional(),
});

export async function POST(req: NextRequest) {
  return adminMutation(req, { 
    permissions: ["projects.write"],
    audit: { action: "create_project", resource: "project" }
  }, async (user, body) => {
    const validatedData = createProjectSchema.parse(body);
    
    const project = await prisma.project.create({
      data: {
        ...validatedData,
        tenantId: user.tenantId || validatedData.tenantId,
      },
      select: SAFE_PROJECT_SELECT, // Prevent data leakage
    });
    
    return project;
  });
}
```

### Implementing Tenant Isolation

**Always scope data access by tenant**:

```typescript
// Correct: Tenant-scoped query
const projects = await prisma.project.findMany({
  where: {
    tenantId: user.tenantId, // Required for multi-tenant security
    deletedAt: null,
  },
});

// Incorrect: No tenant scoping (security vulnerability)
const projects = await prisma.project.findMany({
  where: { deletedAt: null }, // Allows cross-tenant access
});
```

**Use tenantWhere helper**:
```typescript
import { tenantWhere } from "@/lib/admin/guards";

const project = await prisma.project.findFirst({
  where: {
    id: projectId,
    ...tenantWhere(user), // Automatic tenant scoping
    deletedAt: null,
  },
});
```

### Input Validation with Zod

**Define schemas at route level**:
```typescript
const createUserSchema = z.object({
  name: z.string().min(1, "Name is required"),
  email: z.string().email("Invalid email"),
  password: z.string().min(8, "Password must be at least 8 characters"),
  roleIds: z.array(z.string()).optional(),
});
```

**Validate early in mutations**:
```typescript
const validatedData = createUserSchema.parse(body);
// Safe to use validatedData - fully typed and validated
```

### Safe Data Selection

**Never select sensitive fields**:
```typescript
// Use predefined safe selects
import { SAFE_USER_WITH_ROLES_SELECT } from "@/lib/security/safe-user";

const users = await prisma.user.findMany({
  select: SAFE_USER_WITH_ROLES_SELECT, // Excludes passwordHash, etc.
});
```

**Define safe selects in dedicated files**:
```typescript
// lib/security/safe-user.ts
export const SAFE_USER_SELECT = {
  id: true,
  name: true,
  email: true,
  createdAt: true,
  updatedAt: true,
  // Explicitly exclude: passwordHash, passwordHistory, etc.
};
```

## Security Implementation Examples

### Complete Admin API Route

```typescript
// app/api/admin/services/route.ts
import { NextRequest } from "next/server";
import { adminRead, adminMutation } from "@/lib/admin/route";
import { prisma } from "@/lib/prisma";
import { tenantWhere } from "@/lib/admin/guards";
import * as z from "zod";

export const dynamic = "force-dynamic";

const createServiceSchema = z.object({
  name: z.string().min(1, "Name is required"),
  description: z.string().optional(),
  price: z.number().min(0, "Price must be non-negative"),
  category: z.string().optional(),
});

// List services with tenant isolation
export async function GET(req: NextRequest) {
  return adminRead(req, { permissions: ["services.read"] }, async (user) => {
    const services = await prisma.service.findMany({
      where: {
        ...tenantWhere(user),
        deletedAt: null,
      },
      orderBy: { createdAt: "desc" },
      select: {
        id: true,
        name: true,
        description: true,
        price: true,
        category: true,
        createdAt: true,
      },
    });

    return services;
  });
}

// Create service with full security
export async function POST(req: NextRequest) {
  return adminMutation(req, { 
    permissions: ["services.write"],
    audit: { action: "create_service", resource: "service" }
  }, async (user, body) => {
    const validatedData = createServiceSchema.parse(body);

    const service = await prisma.service.create({
      data: {
        ...validatedData,
        tenantId: user.tenantId,
      },
      select: {
        id: true,
        name: true,
        description: true,
        price: true,
        category: true,
        createdAt: true,
      },
    });

    return service;
  });
}
```

### Advanced: Rate Limiting and Audit

```typescript
export async function POST(req: NextRequest) {
  return adminMutation(req, { 
    permissions: ["invoices.write"],
    rateLimitKey: "invoice-creation",
    rateLimitMax: 50,           // Max 50 invoices per user per hour
    rateLimitWindowSec: 3600,
    audit: { 
      action: "create_invoice", 
      resource: "invoice",
      resourceId: "auto" // Will be extracted from response
    }
  }, async (user, body) => {
    // Implementation
  });
}
```

### Error Handling and Security

```typescript
export async function POST(req: NextRequest) {
  try {
    // CSRF protection happens in adminMutation wrapper
    const actor = await requireAdmin({ permissions: ["users.write"] });
    
    const validatedData = createUserSchema.parse(body);
    
    // Business logic with security checks
    const existingUser = await prisma.user.findUnique({
      where: { email: validatedData.email },
    });
    
    if (existingUser) {
      return customNextResponse({ error: "User already exists" }, { status: 409 });
    }
    
    // Secure password handling
    const passwordError = await validatePasswordEnhanced(validatedData.password);
    if (passwordError) {
      return customNextResponse({ error: passwordError }, { status: 400 });
    }
    
    const passwordHash = await bcrypt.hash(validatedData.password, 10);
    
    const newUser = await prisma.user.create({
      data: {
        name: validatedData.name,
        email: validatedData.email,
        passwordHash,
        tenantId: actor.tenantId,
        // Additional security fields
      },
      select: SAFE_USER_WITH_ROLES_SELECT,
    });
    
    // Audit logging for security events
    await createAuditLog({
      action: "create_user",
      resource: "user",
      resourceId: newUser.id,
      actorId: actor.id,
      actorEmail: actor.email,
      after: newUser,
    });
    
    return customNextResponse(newUser, { status: 201 });
  } catch (error) {
    if (error instanceof z.ZodError) {
      return customNextResponse({ error: error.errors }, { status: 400 });
    }
    
    // Security: Don't leak internal errors
    console.error("[API] User creation error:", error);
    return customNextResponse({ error: "Internal server error" }, { status: 500 });
  }
}
```

## Advanced Patterns

### Pagination with Security

```typescript
export async function GET(req: NextRequest) {
  return adminRead(req, { permissions: ["leads.read"] }, async (user) => {
    const { searchParams } = new URL(req.url);
    const pagination = parsePaginationParams(searchParams);
    
    const [leads, total] = await Promise.all([
      prisma.lead.findMany({
        where: {
          ...tenantWhere(user), // Security: tenant isolation
          deletedAt: null,
        },
        take: pagination.take + 1,
        skip: pagination.cursor ? 1 : 0,
        cursor: pagination.cursor ? { id: pagination.cursor } : undefined,
        orderBy: { createdAt: "desc" },
        select: SAFE_LEAD_SELECT, // Security: safe field selection
      }),
      prisma.lead.count({
        where: {
          ...tenantWhere(user),
          deletedAt: null,
        },
      }),
    ]);
    
    return buildPaginationResult(leads, pagination, total);
  });
}
```

### Bulk Operations with Audit

```typescript
export async function POST(req: NextRequest) {
  return adminMutation(req, { 
    permissions: ["leads.write"],
    audit: { action: "bulk_import_leads", resource: "lead" }
  }, async (user, body) => {
    const { leads } = z.object({
      leads: z.array(z.object({
        name: z.string(),
        email: z.string().email(),
        company: z.string().optional(),
      }))
    }).parse(body);
    
    const results = [];
    for (const leadData of leads) {
      try {
        const lead = await prisma.lead.create({
          data: {
            ...leadData,
            tenantId: user.tenantId,
          },
          select: SAFE_LEAD_SELECT,
        });
        results.push({ success: true, data: lead });
      } catch (error) {
        results.push({ success: false, error: error.message });
      }
    }
    
    return { results, total: leads.length };
  });
}
```

## Security Checklist

Before deploying any admin API:

- [ ] **Authentication**: Uses `requireAdmin` or wrapper
- [ ] **Authorization**: Checks appropriate permissions
- [ ] **CSRF Protection**: Same-origin validation + token verification
- [ ] **Input Validation**: Zod schemas for all inputs
- [ ] **Tenant Isolation**: All queries scoped by `tenantId`
- [ ] **Safe Selects**: No sensitive fields exposed
- [ ] **Audit Logging**: All mutations logged with before/after
- [ ] **Rate Limiting**: Applied to sensitive operations
- [ ] **Error Handling**: No sensitive data in error responses
- [ ] **Cache Control**: `no-store` for sensitive data

## Common Security Issues & Solutions

### Issue: Cross-tenant Data Access
```typescript
// VULNERABLE: No tenant scoping
const projects = await prisma.project.findMany();

// SECURE: Tenant-scoped
const projects = await prisma.project.findMany({
  where: { tenantId: user.tenantId }
});
```

### Issue: Sensitive Data Exposure
```typescript
// VULNERABLE: Exposes password hashes
const user = await prisma.user.findUnique({ where: { id } });

// SECURE: Safe select
const user = await prisma.user.findUnique({
  where: { id },
  select: SAFE_USER_SELECT
});
```

### Issue: Missing CSRF Protection
```typescript
// VULNERABLE: No CSRF protection
export async function POST(req: NextRequest) {
  const user = await requireAdmin({ permissions: ["write"] });
  // Implementation
}

// SECURE: CSRF protected
export async function POST(req: NextRequest) {
  return adminMutation(req, { permissions: ["write"] }, async (user, body) => {
    // Implementation
  });
}
```

### Issue: Insufficient Audit Logging
```typescript
// VULNERABLE: No audit trail
await prisma.user.delete({ where: { id } });

// SECURE: Audited deletion
await createAuditLog({
  action: "delete_user",
  resource: "user", 
  resourceId: id,
  actorId: user.id,
  before: existingUser, // Capture before state
});
await prisma.user.delete({ where: { id } });
```

## Testing Security

### Unit Tests for API Security

```typescript
describe("/api/admin/users", () => {
  it("should enforce tenant isolation", async () => {
    const user1 = await createTestUser({ tenantId: "tenant1" });
    const user2 = await createTestUser({ tenantId: "tenant2" });
    
    const response = await apiCall("/api/admin/users", {
      headers: { Authorization: `Bearer ${user1.token}` }
    });
    
    expect(response.users).toContain(user1);
    expect(response.users).not.toContain(user2);
  });
  
  it("should validate CSRF tokens", async () => {
    const response = await apiCall("/api/admin/users", {
      method: "POST",
      headers: { "X-CSRF-Token": "invalid" },
      body: validUserData
    });
    
    expect(response.status).toBe(403);
  });
  
  it("should audit all mutations", async () => {
    await apiCall("/api/admin/users", {
      method: "POST",
      headers: validHeaders,
      body: validUserData
    });
    
    const auditLog = await prisma.auditLog.findFirst({
      where: { action: "create_user" }
    });
    
    expect(auditLog).toBeTruthy();
    expect(auditLog.after).toMatchObject(validUserData);
  });
});
```

### Integration Tests

```typescript
describe("Admin API Security", () => {
  it("should prevent unauthorized access", async () => {
    const response = await fetch("/api/admin/users", {
      headers: { Authorization: "Bearer invalid-token" }
    });
    
    expect(response.status).toBe(401);
  });
  
  it("should enforce RBAC", async () => {
    const limitedUser = await createTestUser({ 
      permissions: ["users.read"] // No write permission
    });
    
    const response = await apiCall("/api/admin/users", {
      method: "POST",
      headers: { Authorization: `Bearer ${limitedUser.token}` },
      body: validUserData
    });
    
    expect(response.status).toBe(403);
  });
});
```

## Performance & Security Trade-offs

### Caching Considerations

- **Sensitive data**: Always use `Cache-Control: no-store`
- **Public data**: Consider `max-age` with appropriate TTL
- **User-specific data**: Use private caching

```typescript
// Sensitive admin data - no caching
return NextResponse.json(data, {
  headers: { "Cache-Control": "no-store, max-age=0" }
});

// Public reference data - cached
return NextResponse.json(data, {
  headers: { "Cache-Control": "max-age=300, private" } // 5 min cache
});
```

### Rate Limiting Strategy

- **Read operations**: Higher limits (1000/hour)
- **Write operations**: Lower limits (100/hour)  
- **Sensitive operations**: Very low limits (10/hour)
- **Bulk operations**: Moderate limits with monitoring

## Migration Guide

### Converting Legacy Routes

**Before (insecure)**:
```typescript
export async function POST(req: NextRequest) {
  const user = await requireAdmin({ permissions: ["write"] });
  const body = await req.json();
  // No CSRF, audit, or rate limiting
}
```

**After (secure)**:
```typescript
export async function POST(req: NextRequest) {
  return adminMutation(req, { 
    permissions: ["write"],
    audit: { action: "create", resource: "resource" }
  }, async (user, body) => {
    // Implementation
  });
}
```

## Next Steps

1. **Review existing APIs** against the security checklist
2. **Implement missing security controls** using the patterns above
3. **Add comprehensive tests** for security scenarios
4. **Document API security requirements** for your team
5. **Set up monitoring** for security events and anomalies

This skill ensures all admin APIs follow Vantus Systems' hardened security standards, protecting against common web vulnerabilities while maintaining enterprise-grade auditability and compliance.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/artisanclarinets) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

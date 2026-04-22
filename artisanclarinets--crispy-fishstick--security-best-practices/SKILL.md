---
name: security-best-practices
description: Implement enterprise-grade security in Next.js systems with RBAC, CSRF protection, CSP, audit logging, and tenant isolation. Use when securing admin portals, APIs, and multi-tenant applications. Use when this capability is needed.
metadata:
  author: artisanclarinets
---

# Security Best Practices

A comprehensive skill for implementing enterprise-grade security in Next.js 16 + React 19 App Router systems, following Fortune-500 security standards with RBAC, CSRF protection, CSP, audit logging, and tenant isolation.

## Quick Start

Secure an admin API endpoint with RBAC, CSRF, and audit logging:

1. **API Route Protection** (`app/api/admin/users/route.ts`):
   ```typescript
   import { adminMutation } from "@/lib/admin/route";

   export async function POST(req: NextRequest) {
     return adminMutation(req, {
       permissions: ["users.write"],
       audit: { action: "create_user", resource: "user" }
     }, async (user, body) => {
       // Business logic here
       const newUser = await prisma.user.create({ data: body });
       return { data: newUser, status: 201 };
     });
   }
   ```

2. **Client-Side API Calls** (`components/admin/user-form.tsx`):
   ```typescript
   import { fetchWithCsrf } from "@/lib/fetchWithCsrf";

   const onSubmit = async (data) => {
     const res = await fetchWithCsrf("/api/admin/users", {
       method: "POST",
       body: JSON.stringify(data),
     });

     if (res.ok) {
       toast.success("User created");
       router.refresh();
     }
   };
   ```

3. **Server-Side Permission Checks** (`app/(admin)/admin/users/page.tsx`):
   ```typescript
   import { requireAdmin } from "@/lib/admin/guards";

   export default async function UsersPage() {
     await requireAdmin({ permissions: ["users.read"] });

     const users = await prisma.user.findMany();
     return <UsersTable users={users} />;
   }
   ```

## Core Concepts

### Role-Based Access Control (RBAC)

- **Permission-Based Access**: Every admin operation requires explicit permissions
- **Tenant Scoping**: Users are restricted to their tenant's data
- **JIT Access**: Just-in-time permissions for temporary elevated access
- **Server Enforcement**: All security checks happen on the server

### CSRF Protection

- **Double-Submit Pattern**: Token validation on both cookie and header
- **Automatic Token Management**: Client-side helper fetches and includes tokens
- **Same-Origin Enforcement**: Strict origin validation for mutations
- **Timing-Safe Comparison**: Prevents timing attacks on token validation

### Content Security Policy (CSP)

- **Nonce-Based Scripts**: Dynamic nonce generation for each request
- **Strict Defaults**: Deny-by-default with explicit allow lists
- **Environment-Specific**: Development vs production CSP rules
- **Frame Protection**: Prevents clickjacking attacks

### Audit Logging

- **Immutable Records**: All privileged operations are logged
- **Sensitive Data Redaction**: Passwords and secrets are never logged
- **Context Capture**: IP, User-Agent, timestamps, and request IDs
- **Before/After States**: Change tracking for data modifications

### Tenant Isolation

- **Database Scoping**: Queries automatically filtered by tenant
- **Permission Wildcards**: Global admins bypass tenant restrictions
- **Cross-Tenant Prevention**: ID-based access always scoped to tenant
- **Audit Boundaries**: Tenant-aware audit logging

## Workflows

### 1. Securing Admin API Endpoints

**Purpose**: Protect administrative operations with proper authentication, authorization, and audit trails.

**Steps**:
1. Use `adminMutation` or `adminRead` wrapper functions
2. Specify required permissions and audit metadata
3. Implement business logic in the handler function
4. Return structured response with data and status

**Example**: User Creation Endpoint
```typescript
export async function POST(req: NextRequest) {
  return adminMutation(req, {
    permissions: ["users.write"],
    audit: { action: "create_user", resource: "user" }
  }, async (user, body) => {
    const validatedData = userCreateSchema.parse(body);
    
    const newUser = await prisma.user.create({
      data: {
        ...validatedData,
        tenantId: user.tenantId, // Automatic tenant scoping
      },
    });

    return { data: newUser, status: 201 };
  });
}
```

### 2. Implementing Permission Checks in Components

**Purpose**: Control UI visibility and functionality based on user permissions.

**Steps**:
1. Use `requireAdmin` in server components for page-level access
2. Use `useAdmin` hook in client components for conditional rendering
3. Check specific permissions before showing admin features
4. Provide appropriate fallbacks for unauthorized users

**Example**: Permission-Gated Admin Dashboard
```typescript
export default async function AdminDashboard() {
  const user = await requireAdmin({ permissions: ["admin.access"] });
  
  const stats = await Promise.all([
    prisma.user.count({ where: tenantWhere(user) }),
    prisma.auditLog.findMany({ 
      take: 5,
      where: tenantWhere(user),
      orderBy: { createdAt: "desc" }
    }),
  ]);

  return (
    <div>
      <h1>Admin Dashboard</h1>
      <StatsCards userCount={stats[0]} />
      <AuditList logs={stats[1]} />
    </div>
  );
}
```

### 3. CSRF-Protected Form Submissions

**Purpose**: Secure form submissions against cross-site request forgery attacks.

**Steps**:
1. Always use `fetchWithCsrf` for admin API calls
2. Include CSRF tokens automatically in headers
3. Handle token refresh and error scenarios
4. Provide user feedback for failed submissions

**Example**: Secure User Update Form
```typescript
"use client";

export function UserUpdateForm({ userId, initialData }) {
  const { hasPermission } = useAdmin();
  
  const onSubmit = async (data) => {
    if (!hasPermission("users.write")) {
      toast.error("Insufficient permissions");
      return;
    }

    try {
      const res = await fetchWithCsrf(`/api/admin/users/${userId}`, {
        method: "PATCH",
        body: JSON.stringify(data),
      });

      if (res.ok) {
        toast.success("User updated");
        router.refresh();
      } else {
        toast.error("Update failed");
      }
    } catch (error) {
      toast.error("Network error");
    }
  };

  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(onSubmit)}>
        {/* Form fields */}
        <Button type="submit">Update User</Button>
      </form>
    </Form>
  );
}
```

### 4. Tenant-Scoped Database Queries

**Purpose**: Ensure data isolation between tenants in multi-tenant applications.

**Steps**:
1. Use `tenantWhere` helper for all tenant-owned resources
2. Include tenant filtering in Prisma queries
3. Validate tenant access in API endpoints
4. Log tenant context in audit records

**Example**: Tenant-Scoped User Query
```typescript
export async function GET(req: NextRequest) {
  return adminRead(req, { permissions: ["users.read"] }, async (user) => {
    const users = await prisma.user.findMany({
      where: {
        ...tenantWhere(user), // Automatic tenant filtering
        deletedAt: null,
      },
      select: SAFE_USER_WITH_ROLES_SELECT,
    });

    return { data: users, status: 200 };
  });
}
```

## Advanced Features

### Audit Log Integration

Automatic audit logging for all admin mutations:

```typescript
// In adminMutation wrapper
if (options.audit && response.ok) {
  await createAuditLog({
    action: options.audit.action,
    resource: options.audit.resource,
    resourceId,
    before: beforeState,
    after: afterState,
    actorId: user.id,
    actorEmail: user.email,
  });
}
```

### CSP Nonce Management

Dynamic nonce generation in proxy middleware:

```typescript
// In proxy.ts
const nonce = crypto.randomUUID().replace(/-/g, "");
const scriptSrc = `script-src 'self' 'nonce-${nonce}'`;

response.headers.set("Content-Security-Policy", csp);
requestHeaders.set("x-nonce", nonce); // Pass to app
```

### Permission Inheritance

Role-based permission system with JIT access:

```typescript
// In getSessionUser
const rolePermissions = user.RoleAssignment.flatMap(r => 
  JSON.parse(r.Role.permissions)
);

const jitPermissions = jitRoles.flatMap(r => 
  JSON.parse(r.permissions)
);

const allPermissions = Array.from(new Set([
  ...rolePermissions, 
  ...jitPermissions
]));
```

## Troubleshooting

### Common Issues

**CSRF Token Errors**: Ensure `fetchWithCsrf` is used for all admin API calls, never regular `fetch`.

**Permission Denied**: Verify user has required permissions and `requireAdmin` is called with correct permissions array.

**Tenant Access Issues**: Check that `tenantWhere` is applied to all tenant-scoped queries.

**Audit Log Failures**: Audit logging failures don't block operations unless `failClosed: true` is set.

**CSP Violations**: Add required domains to CSP configuration in `proxy.ts` for external resources.

## Best Practices

- Always use `adminMutation`/`adminRead` for admin API endpoints
- Implement permission checks at both API and UI levels
- Use `fetchWithCsrf` for all admin API calls
- Apply `tenantWhere` to all tenant-scoped database queries
- Include audit metadata for all privileged operations
- Redact sensitive data before logging
- Test security features with different user roles
- Monitor audit logs for suspicious activity

## Examples

See the codebase for complete implementations:
- [`lib/admin/guards.ts`](lib/admin/guards.ts:1) - RBAC and tenant isolation
- [`lib/security/csrf.ts`](lib/security/csrf.ts:1) - CSRF token management
- [`lib/fetchWithCsrf.ts`](lib/fetchWithCsrf.ts:1) - CSRF-aware fetch helper
- [`lib/admin/audit.ts`](lib/admin/audit.ts:1) - Audit logging system
- [`proxy.ts`](proxy.ts:1) - CSP and security headers
- [`app/api/admin/users/route.ts`](app/api/admin/users/route.ts:1) - Secured admin API

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/artisanclarinets) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

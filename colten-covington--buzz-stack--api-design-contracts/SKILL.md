---
name: api-design-contracts
description: RESTful and type-safe API design, contracts, versioning, and integration patterns for Buzz Stack. Use when this capability is needed.
metadata:
  author: colten-covington
---

# API Design & Contracts

## Overview

Well-designed APIs are contracts between client and server. TypeScript makes these contracts explicit and enforceable at compile time, reducing integration bugs and documentation friction.

**Why it matters:**

- Clear contracts prevent miscommunication
- Type-safe APIs catch errors at build time
- Versioning strategies prevent breaking changes
- Testing contracts ensures reliability

## Core Concepts

### 1. Resource-Based Design (RESTful)

```typescript
// ❌ WRONG: Verb-based (doesn't scale)
// GET /getUsers
// POST /createUser
// PUT /updateUser
// DELETE /removeUser

// ✅ CORRECT: Resource-based
// GET    /api/users        - List users
// POST   /api/users        - Create user
// GET    /api/users/:id    - Get user
// PUT    /api/users/:id    - Update user
// DELETE /api/users/:id    - Delete user

// Consistent structure, clear semantics
```

### 2. Type-Safe Contracts (Buzzstack Pattern)

```typescript
// Define the contract UPFRONT
interface UserContract {
  // Request shape
  requests: {
    create: {
      body: { name: string; email: string };
      response: { id: string; name: string; email: string };
    };
    update: {
      params: { id: string };
      body: Partial<Omit<User, "id">>;
      response: User;
    };
    delete: {
      params: { id: string };
      response: { success: boolean };
    };
  };
}

// Implement with type safety
class UserAPI implements UserContract["requests"] {
  async create(req: UserContract["requests"]["create"]["body"]) {
    // Implementation knows exactly what client sends
    const user = new User(req.name, req.email);
    return user; // Type: Matches response contract
  }
}

// Client knows exactly what to send
const api = new UserAPI();
const user = await api.create({ name: "Alice", email: "alice@example.com" });
// Type: { id: string; name: string; email: string }
```

### 3. Error Handling Contract

```typescript
// Define error shape across API
type ApiError =
  | { code: "VALIDATION_ERROR"; fields: Record<string, string> }
  | { code: "NOT_FOUND"; resource: string; id: string }
  | { code: "UNAUTHORIZED"; reason: string }
  | { code: "SERVER_ERROR"; message: string };

// Response envelope
type ApiResponse<T> = { ok: true; data: T } | { ok: false; error: ApiError };

// Usage
async function fetchUser(id: string): Promise<ApiResponse<User>> {
  try {
    const response = await fetch(`/api/users/${id}`);
    if (!response.ok) {
      if (response.status === 404) {
        return {
          ok: false,
          error: { code: "NOT_FOUND", resource: "User", id },
        };
      }
    }
    return { ok: true, data: await response.json() };
  } catch (error) {
    return {
      ok: false,
      error: { code: "SERVER_ERROR", message: "Unknown error" },
    };
  }
}
```

### 4. Pagination Contract

```typescript
// Define pagination shape
interface PaginatedResponse<T> {
  data: T[];
  pagination: {
    total: number;
    limit: number;
    offset: number;
    hasMore: boolean;
  };
}

// Usage
async function getUsers(
  limit = 20,
  offset = 0,
): Promise<PaginatedResponse<User>> {
  const response = await fetch(`/api/users?limit=${limit}&offset=${offset}`);
  return response.json();
}

// Type-safe pagination
const users = await getUsers(10, 0);
for (const user of users.data) {
  console.log(user.name);
}
if (users.pagination.hasMore) {
  const next = await getUsers(10, users.pagination.offset + 10);
}
```

### 5. Caching Headers & Contracts

```typescript
// Define cache contract
interface CacheContract {
  users: {
    ttl: 300; // 5 minutes
    invalidation: "on-write"; // Invalidate on POST/PUT/DELETE
  };
  posts: {
    ttl: 600; // 10 minutes
    invalidation: "on-write";
  };
}

// Implementation
type CacheKey = keyof typeof CacheContract;

class CachedAPI {
  private cache = new Map<CacheKey, { data: unknown; expires: number }>();

  async getUsers(): Promise<User[]> {
    const key: CacheKey = "users";
    const cached = this.cache.get(key);

    if (cached && cached.expires > Date.now()) {
      return cached.data as User[];
    }

    const data = await fetch("/api/users").then((r) => r.json());
    this.cache.set(key, {
      data,
      expires: Date.now() + CacheContract.users.ttl * 1000,
    });

    return data;
  }
}
```

## Deep Patterns

### Pattern 1: API Versioning

```typescript
// Define versioned contracts
namespace ApiV1 {
  export interface User {
    id: string;
    name: string;
  }

  export interface Post {
    id: string;
    title: string;
    authorId: string;
  }
}

namespace ApiV2 {
  export interface User {
    id: string;
    name: string;
    email: string; // New in v2
  }

  export interface Post {
    id: string;
    title: string;
    author: User; // Relations expanded in v2
  }
}

// Client chooses version
type CurrentAPI = ApiV2;
const user: CurrentAPI["User"] = {
  id: "1",
  name: "Alice",
  email: "alice@example.com",
};
```

### Pattern 2: Typed Query Parameters

```typescript
// Define filter contract
interface UserFilterContract {
  search?: string;
  status?: "active" | "inactive";
  limit?: number;
  offset?: number;
}

// Strictly enforce at the type level
function buildUserQueryString(filters: UserFilterContract): string {
  const params = new URLSearchParams();

  if (filters.search) params.append("search", filters.search);
  if (filters.status) params.append("status", filters.status);
  if (filters.limit) params.append("limit", String(filters.limit));
  if (filters.offset) params.append("offset", String(filters.offset));

  return params.toString();
}

// Type-safe usage
const query = buildUserQueryString({
  search: "Alice",
  status: "active", // ✓ Valid
  limit: 10,
  // Invalid: status: 'banned' would be a compile error
});
```

### Pattern 3: Webhook Contracts

```typescript
// Define webhook event shapes
type WebhookEvent =
  | { type: "user.created"; data: User }
  | { type: "user.updated"; data: User; previousData: User }
  | { type: "user.deleted"; data: { id: string } }
  | { type: "post.published"; data: Post };

// Handler for each event type
function handleWebhookEvent(event: WebhookEvent) {
  switch (event.type) {
    case "user.created":
      console.log(`New user: ${event.data.name}`); // event.data guaranteed User
      break;
    case "user.updated":
      console.log(`Updated: ${event.previousData.name} → ${event.data.name}`);
      break;
    case "user.deleted":
      console.log(`Deleted user ${event.data.id}`);
      break;
    case "post.published":
      console.log(`New post: ${event.data.title}`);
      break;
  }
}
```

### Pattern 4: Mutation Tracking

```typescript
// Track what changed between requests
type Mutation<T> = {
  [K in keyof T]?: {
    before: T[K];
    after: T[K];
    changed: boolean;
  };
};

// Usage
interface User {
  id: string;
  name: string;
  email: string;
}

async function updateUser(
  id: string,
  updates: Partial<User>,
): Promise<{ user: User; mutations: Mutation<User> }> {
  const before = await fetchUser(id);
  const after = { ...before, ...updates };

  const mutations: Mutation<User> = {};
  for (const key in after) {
    const k = key as keyof User;
    if (before[k] !== after[k]) {
      mutations[k] = {
        before: before[k],
        after: after[k],
        changed: true,
      };
    }
  }

  return { user: after, mutations };
}
```

## Anti-Patterns to Avoid

```typescript
// ❌ Wrong: Verb-based endpoints
// POST /api/deleteUser
// GET /api/getUsers

// ✅ Correct: Resource-based
// DELETE /api/users/:id
// GET /api/users

// ❌ Wrong: Inconsistent response shapes
// Sometimes: { user: {...} }
// Sometimes: { data: {...} }

// ✅ Correct: Consistent envelope
// Always: { ok: true; data: {...} } or { ok: false; error: {...} }

// ❌ Wrong: No error contract
// Response could be anything on error

// ✅ Correct: Defined error types
type ApiError = { code: "NOT_FOUND" | "VALIDATION_ERROR"; message: string };

// ❌ Wrong: Changing response shape
// API v1: { id, name }
// API v2: { id, name, email } (breaks clients)

// ✅ Correct: Version your API
// /api/v1/users vs /api/v2/users
```

## HTTP Status Code Contract

```typescript
// Define status codes per operation
const StatusCodes = {
  // Success
  OK: 200, // GET, PUT
  CREATED: 201, // POST (resource created)
  ACCEPTED: 202, // POST (async operation)
  NO_CONTENT: 204, // DELETE

  // Client Error
  BAD_REQUEST: 400, // Invalid input
  UNAUTHORIZED: 401, // Not authenticated
  FORBIDDEN: 403, // No permission
  NOT_FOUND: 404, // Resource not found
  CONFLICT: 409, // Resource already exists
  UNPROCESSABLE: 422, // Validation failed

  // Server Error
  SERVER_ERROR: 500, // Unexpected error
  NOT_IMPLEMENTED: 501, // Feature not available
  UNAVAILABLE: 503, // Service down
} as const;
```

## Real-World Application in Buzz Stack

**Example: Search API**

```typescript
// Type-safe contract
namespace SearchAPI {
  export interface Request {
    query: string;
    filters?: {
      category?: string;
      dateRange?: [string, string];
    };
    pagination?: {
      limit?: number;
      offset?: number;
    };
  }

  export interface Result {
    id: string;
    title: string;
    category: string;
    relevanceScore: number;
  }

  export interface Response {
    results: Result[];
    pagination: {
      total: number;
      hasMore: boolean;
    };
  }
}

// Implementation with contracts
async function search(req: SearchAPI.Request): Promise<SearchAPI.Response> {
  // ...implementation
  return { results: [...], pagination: { total: 0, hasMore: false } };
}
```

## Key Questions When Designing APIs

1. **Is the contract clear and unambiguous?** (Types should make intent obvious)
2. **What happens on error?** (Define error shapes)
3. **How does pagination work?** (Limits, offsets, cursors)
4. **What's the stability guarantee?** (API versioning)
5. **How do I know the data is fresh?** (Caching headers, timestamps)

## Resources

- [REST API Best Practices](https://restfulapi.net/)
- [JSON API Specification](https://jsonapi.org/)
- [OpenAPI/Swagger](https://www.openapis.org/)
- [HTTP Status Codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/colten-covington) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

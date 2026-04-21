---
name: api-integration-enforcer
description: Enforces proper API v1 integration patterns for Token Dashboard backend communication. Ensures correct authentication headers, tenant/project headers, error handling, and response parsing. Use when creating or modifying service layer code, API calls, or debugging API integration issues. Use when this capability is needed.
metadata:
  author: mbelenmontoya
---

# API v1 Integration Patterns

## Purpose

Enforces consistent and correct API integration patterns for Token Dashboard's backend communication. Ensures proper authentication, multi-tenancy headers, error handling, and response parsing.

## When to Use This Skill

- Creating new service files in `src/services/`
- Modifying existing API integration code
- Debugging API request/response issues
- Implementing new API endpoints
- Adding authentication to requests
- Handling multi-tenant API calls

---

## Quick Reference

### API v1 Base Configuration

```javascript
// src/services/http.js
const BASE_URL = import.meta.env.VITE_API_URL || 'http://localhost:4000';
const API_PREFIX = '/api/v1';
```

### Required Headers

**All authenticated requests MUST include:**

```javascript
const headers = {
  'Content-Type': 'application/json',
  'Authorization': `Bearer ${token}`,        // JWT token
  'X-Tenant-ID': tenantId,                    // Multi-tenancy
  'X-Project-ID': projectId                   // Project scoping
};
```

---

## HTTP Wrapper Pattern

### Centralized HTTP Service

```javascript
// src/services/http.js
const BASE_URL = import.meta.env.VITE_API_URL || 'http://localhost:4000';
const API_PREFIX = '/api/v1';

/**
 * Make authenticated API request
 * @param {string} endpoint - API endpoint (without base URL or prefix)
 * @param {object} options - Fetch options
 * @param {string} token - JWT authentication token
 * @param {string} tenantId - Tenant ID for multi-tenancy
 * @param {string} projectId - Project ID for scoping (optional)
 * @returns {Promise<any>} Response data
 */
export async function apiRequest(endpoint, options = {}, token, tenantId, projectId = null) {
  const url = `${BASE_URL}${API_PREFIX}${endpoint}`;

  const headers = {
    'Content-Type': 'application/json',
    ...options.headers
  };

  if (token) {
    headers['Authorization'] = `Bearer ${token}`;
  }

  if (tenantId) {
    headers['X-Tenant-ID'] = tenantId;
  }

  if (projectId) {
    headers['X-Project-ID'] = projectId;
  }

  const config = {
    ...options,
    headers
  };

  try {
    const response = await fetch(url, config);

    // Handle non-2xx responses
    if (!response.ok) {
      const error = await response.json().catch(() => ({
        message: `HTTP ${response.status}: ${response.statusText}`
      }));
      throw new Error(error.message || `Request failed with status ${response.status}`);
    }

    // Handle 204 No Content
    if (response.status === 204) {
      return null;
    }

    return await response.json();
  } catch (error) {
    console.error(`API request failed: ${endpoint}`, error);
    throw error;
  }
}

/**
 * GET request
 */
export function get(endpoint, token, tenantId, projectId, params = {}) {
  const queryString = new URLSearchParams(params).toString();
  const fullEndpoint = queryString ? `${endpoint}?${queryString}` : endpoint;

  return apiRequest(fullEndpoint, { method: 'GET' }, token, tenantId, projectId);
}

/**
 * POST request
 */
export function post(endpoint, data, token, tenantId, projectId) {
  return apiRequest(
    endpoint,
    {
      method: 'POST',
      body: JSON.stringify(data)
    },
    token,
    tenantId,
    projectId
  );
}

/**
 * PUT request
 */
export function put(endpoint, data, token, tenantId, projectId) {
  return apiRequest(
    endpoint,
    {
      method: 'PUT',
      body: JSON.stringify(data)
    },
    token,
    tenantId,
    projectId
  );
}

/**
 * DELETE request
 */
export function del(endpoint, token, tenantId, projectId) {
  return apiRequest(
    endpoint,
    { method: 'DELETE' },
    token,
    tenantId,
    projectId
  );
}
```

---

## Service Layer Patterns

### Token Service Example

```javascript
// src/services/tokenService.js
import { get, post, put, del } from './http';

export const tokenService = {
  /**
   * List tokens with pagination and filtering
   */
  async list(token, tenantId, projectId, options = {}) {
    const { page = 1, limit = 10, category, search } = options;

    const params = { page, limit };
    if (category) params.category = category;
    if (search) params.search = search;

    return await get(
      `/tenants/${tenantId}/projects/${projectId}/tokens`,
      token,
      tenantId,
      projectId,
      params
    );
  },

  /**
   * Get single token by ID
   */
  async get(token, tenantId, projectId, tokenId) {
    return await get(
      `/tenants/${tenantId}/projects/${projectId}/tokens/${tokenId}`,
      token,
      tenantId,
      projectId
    );
  },

  /**
   * Create new token
   */
  async create(token, tenantId, projectId, tokenData) {
    return await post(
      `/tenants/${tenantId}/projects/${projectId}/tokens`,
      tokenData,
      token,
      tenantId,
      projectId
    );
  },

  /**
   * Update existing token
   */
  async update(token, tenantId, projectId, tokenId, updates) {
    return await put(
      `/tenants/${tenantId}/projects/${projectId}/tokens/${tokenId}`,
      updates,
      token,
      tenantId,
      projectId
    );
  },

  /**
   * Delete token
   */
  async remove(token, tenantId, projectId, tokenId) {
    return await del(
      `/tenants/${tenantId}/projects/${projectId}/tokens/${tokenId}`,
      token,
      tenantId,
      projectId
    );
  },

  /**
   * Bulk import tokens
   */
  async import(token, tenantId, projectId, tokens) {
    return await post(
      `/tenants/${tenantId}/projects/${projectId}/tokens/import`,
      tokens,
      token,
      tenantId,
      projectId
    );
  },

  /**
   * Get token categories
   */
  getCategories() {
    return [
      'color',
      'typography',
      'spacing',
      'shadow',
      'border',
      'radius',
      'opacity',
      'z-index',
      'timing'
    ];
  }
};
```

### API Keys Service Example

```javascript
// src/services/apiKeyService.js
import { get, post, del } from './http';

export const apiKeyService = {
  /**
   * List API keys for project
   */
  async list(token, projectId) {
    const response = await get(
      `/projects/${projectId}/keys`,
      token,
      null, // tenantId not needed for this endpoint
      projectId
    );

    // Handle both response formats
    return response.keys || response.items || response;
  },

  /**
   * Create new API key
   */
  async create(token, projectId, keyData) {
    const response = await post(
      `/projects/${projectId}/keys`,
      keyData,
      token,
      null,
      projectId
    );

    // Handle both response formats
    return response.apiKey || response.key || response;
  },

  /**
   * Rotate API key
   */
  async rotate(token, projectId, keyId) {
    const response = await post(
      `/projects/${projectId}/keys/${keyId}/rotate`,
      {},
      token,
      null,
      projectId
    );

    return response.apiKey || response.key || response;
  },

  /**
   * Revoke API key
   */
  async revoke(token, projectId, keyId) {
    return await del(
      `/projects/${projectId}/keys/${keyId}`,
      token,
      null,
      projectId
    );
  }
};
```

### Membership Service Example

```javascript
// src/services/membershipService.js
import { get } from './http';

export const membershipService = {
  /**
   * Get current user's memberships
   */
  async getMyMemberships(token) {
    const response = await get(
      '/users/me/memberships',
      token,
      null, // No tenant scoping for this endpoint
      null
    );

    return response.memberships || response;
  }
};
```

---

## Error Handling Patterns

### Service-Level Error Handling

```javascript
// In service file
try {
  const result = await tokenService.create(token, tenantId, projectId, tokenData);
  return result;
} catch (error) {
  console.error('Failed to create token:', error);
  throw error; // Re-throw for component to handle
}
```

### Component-Level Error Handling

```javascript
// In React component
const [error, setError] = useState(null);
const [loading, setLoading] = useState(false);

const handleCreate = async (tokenData) => {
  try {
    setLoading(true);
    setError(null);

    const result = await tokenService.create(token, tenantId, projectId, tokenData);

    // Success handling
    onSuccess(result);
  } catch (err) {
    setError(err.message || 'Failed to create token');
  } finally {
    setLoading(false);
  }
};
```

### Error Display Pattern

```jsx
{error && (
  <div className="bg-red-50 dark:bg-red-900/20 border border-red-200 dark:border-red-800 rounded-md p-4 mb-4">
    <p className="text-red-800 dark:text-red-200">{error}</p>
  </div>
)}
```

---

## Authentication Patterns

### Login Service

```javascript
// src/services/authService.js
const BASE_URL = import.meta.env.VITE_API_URL || 'http://localhost:4000';

export const authService = {
  /**
   * Login user
   * Note: Auth endpoints use /api/auth, not /api/v1
   */
  async login(username, password) {
    const response = await fetch(`${BASE_URL}/api/auth/login`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({ username, password })
    });

    if (!response.ok) {
      const error = await response.json().catch(() => ({ message: 'Login failed' }));
      throw new Error(error.message || 'Invalid credentials');
    }

    return await response.json();
  }
};
```

### Token Storage

```javascript
// Store token after login
const { token, user } = await authService.login(username, password);
localStorage.setItem('jwt', token);

// Retrieve token for API calls
const token = localStorage.getItem('jwt');

// Clear token on logout
localStorage.removeItem('jwt');
```

---

## Response Format Handling

### Flexible Response Parsing

Some API endpoints return different response formats. Handle both:

```javascript
// API might return { keys: [...] } or { items: [...] } or [...]
const response = await get(endpoint, token, tenantId, projectId);
const keys = response.keys || response.items || response;

// API might return { token: {...} } or { data: {...} } or {...}
const response = await post(endpoint, data, token, tenantId, projectId);
const token = response.token || response.data || response;
```

---

## Testing API Integration

### MSW Handler Setup

```javascript
// src/mocks/handlers.js
import { http, HttpResponse } from 'msw';

const BASE_URL = 'http://localhost:4000';

export const handlers = [
  http.get(`${BASE_URL}/api/v1/tenants/:tenantId/projects/:projectId/tokens`, ({ request }) => {
    // Check authentication header
    const authHeader = request.headers.get('Authorization');
    if (!authHeader || !authHeader.startsWith('Bearer ')) {
      return HttpResponse.json(
        { error: 'Unauthorized' },
        { status: 401 }
      );
    }

    // Check tenant header
    const tenantId = request.headers.get('X-Tenant-ID');
    if (!tenantId) {
      return HttpResponse.json(
        { error: 'Tenant ID required' },
        { status: 400 }
      );
    }

    return HttpResponse.json({ tokens: mockTokens });
  })
];
```

---

## Common Pitfalls

### ❌ Missing Authentication Header

```javascript
// BAD - No auth header
await fetch('/api/v1/tokens');

// GOOD - Include auth header
await get('/tenants/:id/projects/:id/tokens', token, tenantId, projectId);
```

### ❌ Hardcoded Base URL

```javascript
// BAD - Hardcoded URL
await fetch('http://localhost:4000/api/v1/tokens');

// GOOD - Use environment variable
const BASE_URL = import.meta.env.VITE_API_URL;
```

### ❌ Not Handling 204 No Content

```javascript
// BAD - Assumes JSON response
const data = await response.json();

// GOOD - Check status first
if (response.status === 204) {
  return null;
}
return await response.json();
```

### ❌ Swallowing Errors

```javascript
// BAD - Silent failure
try {
  await apiCall();
} catch (error) {
  // Do nothing
}

// GOOD - Log and re-throw or handle
try {
  await apiCall();
} catch (error) {
  console.error('API call failed:', error);
  throw error;
}
```

---

## Checklist for New Service

When creating a new service file:

- [ ] Import HTTP wrapper functions (`get`, `post`, `put`, `del`)
- [ ] Pass `token` as first parameter to all methods
- [ ] Pass `tenantId` and `projectId` for scoped endpoints
- [ ] Use JSDoc comments for all methods
- [ ] Handle both success and error cases
- [ ] Return consistent data structures
- [ ] Handle flexible response formats (keys vs items vs direct array)
- [ ] Add to MSW handlers for testing
- [ ] Write service-level tests

---

## Resource Files

- [endpoint-reference.md](resources/endpoint-reference.md) - Complete API v1 endpoint list
- [error-codes.md](resources/error-codes.md) - API error codes and handling
- [testing-api-services.md](resources/testing-api-services.md) - Service testing patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mbelenmontoya) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

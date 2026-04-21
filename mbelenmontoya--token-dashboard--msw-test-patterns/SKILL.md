---
name: msw-test-patterns
description: Mock Service Worker (MSW) handlers and Vitest testing patterns for Token Dashboard. Create MSW handlers for API mocking, write Vitest tests for components and services, handle async testing, mock authentication, and achieve test coverage goals. Use when creating tests, mock handlers, or working with test files. Use when this capability is needed.
metadata:
  author: mbelenmontoya
---

# MSW + Vitest Testing Patterns

## Purpose

Complete guide for testing in Token Dashboard using Mock Service Worker (MSW) for API mocking and Vitest for test execution. Critical for completing ROADMAP Stage 1 requirements.

## When to Use This Skill

- Creating MSW handlers for API endpoints
- Writing Vitest tests for components
- Testing services with mocked APIs
- Setting up test fixtures and mock data
- Testing authentication flows
- Achieving test coverage goals
- Debugging failing tests

---

## Quick Start

### ROADMAP Stage 1 Requirements

**Missing MSW handlers** (from ROADMAP.md):
- ❌ Memberships (list)
- ❌ Tokens (list/CRUD/import)
- ❌ API Keys (list/create/rotate/revoke)

**Test coverage goals**:
- Component tests for major features
- Service layer integration tests
- API mocking for all endpoints

---

## MSW Handler Patterns

### Handler File Structure

Token Dashboard stores MSW handlers in:
- `src/mocks/handlers.js` - Main handler file (TO BE CREATED)
- `src/mocks/data/` - Mock data fixtures

### Creating Handlers

#### Step 1: Create Mock Data

```javascript
// src/mocks/data/memberships.js
export const mockMemberships = [
  {
    tenant: {
      id: 'tenant-1',
      name: 'Acme Corp'
    },
    role: 'admin',
    projects: [
      {
        id: 'project-1',
        name: 'Design System'
      }
    ]
  },
  {
    tenant: {
      id: 'tenant-2',
      name: 'Beta Inc'
    },
    role: 'editor',
    projects: [
      {
        id: 'project-2',
        name: 'Website'
      }
    ]
  }
];
```

#### Step 2: Create Handlers

```javascript
// src/mocks/handlers.js
import { http, HttpResponse } from 'msw';
import { mockMemberships } from './data/memberships';
import { mockTokens } from './data/tokens';
import { mockApiKeys } from './data/apiKeys';

const BASE_URL = 'http://localhost:4000';

export const handlers = [
  // Memberships
  http.get(`${BASE_URL}/api/v1/users/me/memberships`, () => {
    return HttpResponse.json({ memberships: mockMemberships });
  }),

  // Tokens - List with pagination
  http.get(`${BASE_URL}/api/v1/tenants/:tenantId/projects/:projectId/tokens`, ({ request, params }) => {
    const url = new URL(request.url);
    const page = parseInt(url.searchParams.get('page') || '1');
    const limit = parseInt(url.searchParams.get('limit') || '10');
    const category = url.searchParams.get('category');
    const search = url.searchParams.get('search');

    let filtered = mockTokens;

    // Apply filters
    if (category) {
      filtered = filtered.filter(t => t.category === category);
    }
    if (search) {
      filtered = filtered.filter(t => t.name.toLowerCase().includes(search.toLowerCase()));
    }

    // Pagination
    const start = (page - 1) * limit;
    const end = start + limit;
    const paginatedTokens = filtered.slice(start, end);

    return HttpResponse.json({
      tokens: paginatedTokens,
      pagination: {
        page,
        limit,
        total: filtered.length,
        totalPages: Math.ceil(filtered.length / limit)
      }
    });
  }),

  // Tokens - Create
  http.post(`${BASE_URL}/api/v1/tenants/:tenantId/projects/:projectId/tokens`, async ({ request }) => {
    const newToken = await request.json();
    return HttpResponse.json(
      { token: { ...newToken, id: `token-${Date.now()}` } },
      { status: 201 }
    );
  }),

  // Tokens - Update
  http.put(`${BASE_URL}/api/v1/tenants/:tenantId/projects/:projectId/tokens/:tokenId`, async ({ request, params }) => {
    const updates = await request.json();
    return HttpResponse.json({
      token: { ...updates, id: params.tokenId }
    });
  }),

  // Tokens - Delete
  http.delete(`${BASE_URL}/api/v1/tenants/:tenantId/projects/:projectId/tokens/:tokenId`, () => {
    return new HttpResponse(null, { status: 204 });
  }),

  // Tokens - Bulk Import
  http.post(`${BASE_URL}/api/v1/tenants/:tenantId/projects/:projectId/tokens/import`, async ({ request }) => {
    const tokens = await request.json();
    return HttpResponse.json({
      imported: tokens.length,
      tokens: tokens.map((t, i) => ({ ...t, id: `imported-${i}` }))
    });
  }),

  // API Keys - List
  http.get(`${BASE_URL}/api/v1/projects/:projectId/keys`, () => {
    return HttpResponse.json({ keys: mockApiKeys });
  }),

  // API Keys - Create
  http.post(`${BASE_URL}/api/v1/projects/:projectId/keys`, async ({ request }) => {
    const keyData = await request.json();
    return HttpResponse.json({
      apiKey: {
        ...keyData,
        id: `key-${Date.now()}`,
        key: `sk_${Math.random().toString(36).substr(2, 9)}`
      }
    }, { status: 201 });
  }),

  // API Keys - Rotate
  http.post(`${BASE_URL}/api/v1/projects/:projectId/keys/:keyId/rotate`, ({ params }) => {
    return HttpResponse.json({
      apiKey: {
        id: params.keyId,
        key: `sk_${Math.random().toString(36).substr(2, 9)}`
      }
    });
  }),

  // API Keys - Revoke
  http.delete(`${BASE_URL}/api/v1/projects/:projectId/keys/:keyId`, () => {
    return new HttpResponse(null, { status: 204 });
  }),

  // Authentication
  http.post(`${BASE_URL}/api/auth/login`, async ({ request }) => {
    const { username, password } = await request.json();
    if (username === 'admin' && password === 'Admin#123') {
      return HttpResponse.json({
        token: 'mock-jwt-token',
        user: { id: '1', username: 'admin', email: 'admin@example.com' }
      });
    }
    return HttpResponse.json(
      { error: 'Invalid credentials' },
      { status: 401 }
    );
  })
];
```

#### Step 3: Setup MSW in Tests

```javascript
// tests/setup.js
import { beforeAll, afterEach, afterAll } from 'vitest';
import { setupServer } from 'msw/node';
import { handlers } from '../src/mocks/handlers';

const server = setupServer(...handlers);

// Start server before all tests
beforeAll(() => server.listen({ onUnhandledRequest: 'error' }));

// Reset handlers after each test
afterEach(() => server.resetHandlers());

// Clean up after all tests
afterAll(() => server.close());

export { server };
```

---

## Vitest Test Patterns

### Component Testing

```javascript
// src/components/__tests__/TokenManager.test.jsx
import { describe, it, expect, beforeEach, vi } from 'vitest';
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import { TokenManager } from '../TokenManager';

describe('TokenManager', () => {
  const mockToken = 'mock-jwt-token';

  beforeEach(() => {
    vi.clearAllMocks();
  });

  it('renders tokens list', async () => {
    render(<TokenManager token={mockToken} tenantId="tenant-1" projectId="project-1" />);

    // Wait for tokens to load
    await waitFor(() => {
      expect(screen.getByTestId('tokens-table')).toBeInTheDocument();
    });

    // Check first token appears
    expect(screen.getByText('primary-blue')).toBeInTheDocument();
  });

  it('filters tokens by category', async () => {
    render(<TokenManager token={mockToken} tenantId="tenant-1" projectId="project-1" />);

    await waitFor(() => {
      expect(screen.getByTestId('tokens-table')).toBeInTheDocument();
    });

    // Click color category filter
    const colorFilter = screen.getByText('Color');
    fireEvent.click(colorFilter);

    // Should only show color tokens
    await waitFor(() => {
      expect(screen.queryByText('font-size-base')).not.toBeInTheDocument();
      expect(screen.getByText('primary-blue')).toBeInTheDocument();
    });
  });

  it('creates new token', async () => {
    render(<TokenManager token={mockToken} tenantId="tenant-1" projectId="project-1" />);

    // Click create button
    const createBtn = screen.getByTestId('create-token-button');
    fireEvent.click(createBtn);

    // Fill form
    const nameInput = screen.getByPlaceholderText('Token name');
    const valueInput = screen.getByPlaceholderText('Token value');

    fireEvent.change(nameInput, { target: { value: 'new-token' } });
    fireEvent.change(valueInput, { target: { value: '#ff0000' } });

    // Submit
    const saveBtn = screen.getByText('Save');
    fireEvent.click(saveBtn);

    // Check token appears in list
    await waitFor(() => {
      expect(screen.getByText('new-token')).toBeInTheDocument();
    });
  });
});
```

### Service Testing

```javascript
// src/services/__tests__/tokenService.test.js
import { describe, it, expect, beforeEach } from 'vitest';
import { tokenService } from '../tokenService';

describe('tokenService', () => {
  const token = 'mock-jwt-token';
  const tenantId = 'tenant-1';
  const projectId = 'project-1';

  it('lists tokens with pagination', async () => {
    const result = await tokenService.list(token, tenantId, projectId, {
      page: 1,
      limit: 10
    });

    expect(result.tokens).toBeDefined();
    expect(result.pagination).toBeDefined();
    expect(result.pagination.page).toBe(1);
    expect(result.pagination.limit).toBe(10);
  });

  it('filters tokens by category', async () => {
    const result = await tokenService.list(token, tenantId, projectId, {
      category: 'color'
    });

    expect(result.tokens).toBeDefined();
    result.tokens.forEach(token => {
      expect(token.category).toBe('color');
    });
  });

  it('creates token', async () => {
    const newToken = {
      name: 'test-token',
      value: '#123456',
      category: 'color',
      description: 'Test token'
    };

    const result = await tokenService.create(token, tenantId, projectId, newToken);

    expect(result.token).toBeDefined();
    expect(result.token.id).toBeDefined();
    expect(result.token.name).toBe(newToken.name);
  });

  it('handles errors gracefully', async () => {
    await expect(
      tokenService.list('invalid-token', tenantId, projectId)
    ).rejects.toThrow();
  });
});
```

### Authentication Testing

```javascript
// src/services/__tests__/authService.test.js
import { describe, it, expect } from 'vitest';
import { authService } from '../authService';

describe('authService', () => {
  it('logs in successfully with valid credentials', async () => {
    const result = await authService.login('admin', 'Admin#123');

    expect(result.token).toBe('mock-jwt-token');
    expect(result.user).toBeDefined();
    expect(result.user.username).toBe('admin');
  });

  it('fails with invalid credentials', async () => {
    await expect(
      authService.login('admin', 'wrong-password')
    ).rejects.toThrow();
  });
});
```

---

## Test Coverage Strategy

### Priority 1: Service Layer (API Integration)

Test all services with MSW handlers:
- `tokenService.js`
- `apiKeyService.js`
- `membershipService.js`
- `authService.js`
- `billingService.js`

### Priority 2: Major Components

Test core UI components:
- `TokenManager.jsx`
- `APIKeys.jsx`
- `ProjectHub.jsx`
- `Billing.jsx`

### Priority 3: Utility Functions

Test helper functions:
- Token validation
- Data transformations
- Error handling utilities

---

## Running Tests

### Commands

```bash
# Run all tests
npm run test

# Run tests in watch mode
npm run test:watch

# Run with coverage
npm run test -- --coverage

# Run specific file
npm run test src/services/__tests__/tokenService.test.js
```

### Coverage Goals

- Service layer: 100% coverage
- Components: 80% coverage
- Overall: 85% coverage

---

## Common Test Patterns

### Async/Await Testing

```javascript
it('handles async operations', async () => {
  const result = await someAsyncFunction();
  expect(result).toBeDefined();
});
```

### waitFor Pattern

```javascript
it('waits for element to appear', async () => {
  render(<Component />);

  await waitFor(() => {
    expect(screen.getByText('Loaded')).toBeInTheDocument();
  });
});
```

### User Event Simulation

```javascript
import { fireEvent } from '@testing-library/react';

it('handles user interactions', () => {
  render(<Component />);

  const button = screen.getByRole('button');
  fireEvent.click(button);

  expect(/* assertion */);
});
```

### Mocking Functions

```javascript
import { vi } from 'vitest';

it('calls callback', () => {
  const callback = vi.fn();
  render(<Component onAction={callback} />);

  // Trigger action
  fireEvent.click(screen.getByRole('button'));

  expect(callback).toHaveBeenCalled();
  expect(callback).toHaveBeenCalledWith(expectedArgs);
});
```

---

## Troubleshooting

### Issue: MSW handlers not working

**Problem**: API calls not being intercepted

**Solution**:
1. Verify MSW server is set up in test setup
2. Check handler URL matches exactly (including base URL)
3. Ensure `server.listen()` is called before tests

### Issue: Tests timing out

**Problem**: waitFor never resolves

**Solution**:
1. Check if MSW handler returns response
2. Verify component is actually making the API call
3. Increase timeout: `waitFor(() => {}, { timeout: 5000 })`

### Issue: Test fails in CI but passes locally

**Problem**: Environment differences

**Solution**:
1. Check MSW is set up for Node environment (`msw/node`)
2. Verify all async operations use `await`
3. Ensure proper cleanup with `afterEach`

---

## Resource Files

- [msw-examples.md](resources/msw-examples.md) - More MSW handler examples
- [vitest-assertions.md](resources/vitest-assertions.md) - Complete assertion reference
- [testing-checklist.md](resources/testing-checklist.md) - Test coverage checklist

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mbelenmontoya) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

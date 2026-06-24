---
name: agile-testing-guide
description: Guide testing strategy and patterns for the current language/framework. Use when planning how to test a feature, choosing between unit/integration/e2e tests, or reviewing test quality across Go or TypeScript projects. Do NOT use for linting test code against Detroit School principles (use detroit-school-lint) or for writing a specific test (use bug-fix-tdd). Use when this capability is needed.
metadata:
  author: toru-oizumi
---

# Agile Testing Guide

Language-adaptive testing strategy guide. Apply the section matching the project's language.

## Language Detection

Identify language from:
- File extensions: `.go` → Go, `.ts`/`.tsx` → TypeScript
- Project files: `go.mod` → Go, `package.json` + `tsconfig.json` → TypeScript

---

## Go

### Test Pyramid

| Layer | Tool | Focus |
|-------|------|-------|
| Unit | `go test` | Business logic, domain functions |
| Handler | `httptest` | HTTP handler behavior |
| Integration | `go test -tags=integration` | Database, external services |

### Patterns

**Table-driven tests**
```go
func TestUserCreate(t *testing.T) {
    tests := []struct {
        name    string
        input   CreateUserInput
        want    *User
        wantErr bool
    }{
        {
            name:  "valid user",
            input: CreateUserInput{Name: "Alice", Email: "alice@example.com"},
            want:  &User{Name: "Alice"},
        },
        {
            name:    "empty name",
            input:   CreateUserInput{Name: ""},
            wantErr: true,
        },
    }
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            got, err := CreateUser(tt.input)
            if (err != nil) != tt.wantErr {
                t.Errorf("wantErr=%v, got err=%v", tt.wantErr, err)
            }
            if !tt.wantErr {
                assert.Equal(t, tt.want.Name, got.Name)
            }
        })
    }
}
```

**Handler tests with httptest**
```go
func TestCreateUserHandler(t *testing.T) {
    repo := &FakeUserRepository{}
    h := NewUserHandler(repo)
    body := `{"name":"Alice","email":"alice@example.com"}`
    req := httptest.NewRequest(http.MethodPost, "/users", strings.NewReader(body))
    req.Header.Set("Content-Type", "application/json")
    w := httptest.NewRecorder()
    h.CreateUser(w, req)
    assert.Equal(t, http.StatusCreated, w.Code)
}
```

**In-memory fakes**
```go
type FakeUserRepository struct {
    users map[string]*User
}

func (f *FakeUserRepository) FindByID(ctx context.Context, id string) (*User, error) {
    u, ok := f.users[id]
    if !ok {
        return nil, ErrNotFound
    }
    return u, nil
}
```

### Test Organization

```
<source_root>/
├── domain/          # Unit tests alongside source
├── usecase/         # Use case tests with fakes
├── handler/         # httptest-based handler tests
└── testutil/        # Shared fakes, fixtures, helpers
```

### Watch-fors

- Always use table-driven tests for business logic with multiple cases
- Use `t.Parallel()` in table-driven subtests when tests are independent
- `sql.Rows.Close()` must be deferred immediately after query in integration tests
- Pass `context.Background()` (or `t.Context()` in Go 1.21+) as first argument

---

## TypeScript / Node.js

### Test Pyramid

| Layer | Tool | Focus |
|-------|------|-------|
| Unit | Vitest | Domain logic, pure functions |
| Integration | Vitest + real DB | Use case + repository |
| API mock | MSW | External HTTP dependencies |
| E2E | Playwright / Supertest | Full request cycle |

### Patterns

**Unit tests with Vitest**
```typescript
import { describe, it, expect } from 'vitest';
import { createUser } from './user-service';

describe('createUser', () => {
  it('throws when name is empty', () => {
    expect(() => createUser({ name: '', email: 'a@b.com' }))
      .toThrow('Name is required');
  });
});
```

**In-memory fakes for use case tests**
```typescript
class InMemoryUserRepository implements UserRepository {
  private users = new Map<string, User>();

  async findById(id: string): Promise<User | null> {
    return this.users.get(id) ?? null;
  }

  async save(user: User): Promise<void> {
    this.users.set(user.id, user);
  }
}
```

**MSW for API mocking (external HTTP only)**
```typescript
import { http, HttpResponse } from 'msw';
import { setupServer } from 'msw/node';

const server = setupServer(
  http.get('/api/external', () => HttpResponse.json({ data: 'mocked' }))
);
beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());
```

**Agent/tool testing (Mastra)**
```typescript
describe('fetchUserTool', () => {
  it('returns user data', async () => {
    const result = await fetchUserTool.execute({ userId: '123' }, context);
    expect(result).toMatchObject({ id: '123' });
  });
});
```

### Test Organization

```
<source_root>/
├── domain/          # Pure unit tests (no I/O)
├── usecase/         # Tests with in-memory fakes
├── infrastructure/  # Integration tests with real DB
└── tests/
    ├── e2e/         # Full request cycle
    └── mocks/       # MSW handlers for external APIs
```

### Watch-fors

- Never mock internal dependencies in use case tests — use in-memory fakes
- MSW is for external HTTP calls only, not internal module mocking
- Use `vi.mock()` only for non-deterministic side effects (Date, crypto)
- E2E tests must run against a real database, not mocks

## Related Skills

- `detroit-school-lint` — Validate tests against Detroit School principles
- `bug-fix-tdd` — Fix bugs by writing a failing test first
- `pr-reviewer-3a` — PR review including test quality check

## Gotchas

See `gotchas.md` in this directory for known pitfalls and recurring mistakes when using this skill.

---
> Source: [toru-oizumi/claude-harness-engineering](https://github.com/toru-oizumi/claude-harness-engineering) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->

---
name: nestjs-testing
description: Unit and E2E testing strategies with Docker. Use when this capability is needed.
metadata:
  author: ngxtm
---

# NestJS Testing Standards

## Strategy

1. **Unit Tests**: Isolated logic (Services). Mock **all** dependencies (`jest.fn()`).
2. **E2E Tests**: Full lifecycle (`test/app.e2e-spec.ts`).
   - **Rule**: Use a **real** Test Database (Docker). Never mock the database in E2E.
   - **Idempotency**: **Mandatory** cleanup. Use a transaction rollback strategy or explicit `TRUNCATE` in `afterEach` to ensure tests don't leak state.

## E2E Best Practices (Pro)

- **Overriding**: Use `.overrideProvider(AuthGuard).useValue({ canActivate: () => true })` to bypass security in functional flow tests.
- **Factories**: Use Factory patterns (e.g. `mockUserFactory()`) to generate DTOs/Entities. Avoid hardcoded JSON literals.

## Tools

- **Runner**: Jest (swc/ts-jest).
- **Mocks**: `jest.fn()`, `jest.spyOn()`.

## Setup Pattern

```typescript
// Standard Setup
const module: TestingModule = await Test.createTestingModule({
  providers: [
    UsersService,
    { provide: getRepositoryToken(User), useValue: mockRepo },
  ],
}).compile();
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ngxtm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

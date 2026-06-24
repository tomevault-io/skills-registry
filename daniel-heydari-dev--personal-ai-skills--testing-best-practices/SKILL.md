---
name: testing-best-practices
description: Write effective, maintainable tests Use when this capability is needed.
metadata:
  author: daniel-heydari-dev
---

# Skill: Testing Best Practices

Write tests that are readable, reliable, and maintainable.

## AAA Pattern

### Rules

- ✅ DO: Structure tests with Arrange, Act, Assert
- ✅ DO: Separate each section with blank lines
- ✅ DO: Keep each section focused
- ❌ DON'T: Mix arrangement and assertion

### Examples

```typescript
it("should calculate total with discount", () => {
  // Arrange
  const items = [
    { price: 100, quantity: 2 },
    { price: 50, quantity: 1 },
  ];
  const discount = 0.1;

  // Act
  const total = calculateTotal(items, discount);

  // Assert
  expect(total).toBe(225); // (200 + 50) * 0.9
});
```

## Test Naming

### Rules

- ✅ DO: Describe behavior, not implementation
- ✅ DO: Use format: "should [behavior] when [condition]"
- ✅ DO: Be specific about expected outcome
- ❌ DON'T: Use vague names like "works correctly"
- ❌ DON'T: Reference implementation details

### Examples

```typescript
// ❌ Bad names
it("test1", () => {});
it("works", () => {});
it("calls the function", () => {});

// ✅ Good names
it("should return empty array when input is empty", () => {});
it("should throw error when email is invalid", () => {});
it("should apply discount when user is premium", () => {});
```

## Test Organization

### Rules

- ✅ DO: Group related tests with `describe`
- ✅ DO: Use nested `describe` for sub-features
- ✅ DO: Keep test files close to source files
- ✅ DO: Use consistent file naming (`*.test.ts`, `*.spec.ts`)

### Examples

```typescript
describe("UserService", () => {
  describe("createUser", () => {
    it("should create user with valid data", () => {});
    it("should throw error when email exists", () => {});
    it("should hash password before saving", () => {});
  });

  describe("deleteUser", () => {
    it("should remove user from database", () => {});
    it("should throw error when user not found", () => {});
  });
});
```

## One Assertion Per Test

### Rules

- ✅ DO: Test one behavior per test
- ✅ DO: Use multiple assertions only when testing same behavior
- ✅ DO: Split into multiple tests if behaviors are different
- ❌ DON'T: Test unrelated things together

### Examples

```typescript
// ❌ Bad - testing multiple behaviors
it("should create user", async () => {
  const user = await createUser(data);
  expect(user.id).toBeDefined();
  expect(user.createdAt).toBeDefined();
  expect(sendEmail).toHaveBeenCalled(); // Different behavior!
  expect(audit.log).toHaveBeenCalled(); // Different behavior!
});

// ✅ Good - separate tests
it("should return user with id after creation", async () => {
  const user = await createUser(data);
  expect(user.id).toBeDefined();
});

it("should send welcome email after creation", async () => {
  await createUser(data);
  expect(sendEmail).toHaveBeenCalledWith(
    expect.objectContaining({
      type: "welcome",
    }),
  );
});
```

## Test Data

### Rules

- ✅ DO: Use factories or builders for test data
- ✅ DO: Only specify data relevant to the test
- ✅ DO: Use realistic but not real data
- ❌ DON'T: Copy-paste test data everywhere
- ❌ DON'T: Use production data in tests

### Examples

```typescript
// ✅ Good - factory function
function createTestUser(overrides: Partial<User> = {}): User {
  return {
    id: "user-123",
    name: "Test User",
    email: "test@example.com",
    role: "user",
    ...overrides,
  };
}

it("should deny access for non-admin users", () => {
  const user = createTestUser({ role: "user" });
  expect(canAccessAdmin(user)).toBe(false);
});

it("should allow access for admin users", () => {
  const user = createTestUser({ role: "admin" });
  expect(canAccessAdmin(user)).toBe(true);
});
```

## Mocking

### Rules

- ✅ DO: Mock external dependencies (API, database, time)
- ✅ DO: Verify mock interactions when relevant
- ✅ DO: Reset mocks between tests
- ❌ DON'T: Over-mock (don't mock what you're testing)
- ❌ DON'T: Mock implementation details

### Examples

```typescript
import { vi, beforeEach, afterEach } from "vitest";

// Mock external service
vi.mock("./emailService", () => ({
  sendEmail: vi.fn(),
}));

beforeEach(() => {
  vi.clearAllMocks();
});

it("should send notification email", async () => {
  const { sendEmail } = await import("./emailService");

  await notifyUser(user, "Welcome!");

  expect(sendEmail).toHaveBeenCalledWith({
    to: user.email,
    subject: "Notification",
    body: "Welcome!",
  });
});

// Mock time
it("should expire after 24 hours", () => {
  vi.useFakeTimers();
  vi.setSystemTime(new Date("2024-01-01"));

  const token = createToken();

  vi.advanceTimersByTime(25 * 60 * 60 * 1000); // 25 hours

  expect(isTokenValid(token)).toBe(false);

  vi.useRealTimers();
});
```

## Async Testing

### Rules

- ✅ DO: Always await async operations
- ✅ DO: Test both success and error cases
- ✅ DO: Use `rejects` matcher for async errors
- ❌ DON'T: Forget to return/await promises

### Examples

```typescript
// ✅ Good - async/await
it("should fetch user data", async () => {
  const user = await fetchUser("123");
  expect(user.name).toBe("John");
});

// ✅ Good - test async errors
it("should throw when user not found", async () => {
  await expect(fetchUser("invalid")).rejects.toThrow("User not found");
});

// ✅ Good - test async with mock
it("should retry on failure", async () => {
  const mockFetch = vi
    .fn()
    .mockRejectedValueOnce(new Error("Network error"))
    .mockResolvedValueOnce({ data: "success" });

  const result = await fetchWithRetry(mockFetch);

  expect(mockFetch).toHaveBeenCalledTimes(2);
  expect(result).toEqual({ data: "success" });
});
```

## Coverage

### Rules

- ✅ DO: Aim for meaningful coverage, not 100%
- ✅ DO: Cover happy paths, edge cases, and error cases
- ✅ DO: Test business logic thoroughly
- ❌ DON'T: Test trivial code (getters, simple pass-through)
- ❌ DON'T: Write tests just to increase coverage

### Priority

1. **Business logic** — Most important
2. **Error handling** — Critical paths
3. **Edge cases** — Boundaries, null, empty
4. **Integration points** — API calls, database
5. **UI components** — User interactions

## Test Pyramid

```
        /\
       /  \        E2E Tests (few)
      /----\       - Full user flows
     /      \      - Slow, expensive
    /--------\     Integration Tests (some)
   /          \    - Component interactions
  /------------\   - API tests
 /              \  Unit Tests (many)
/----------------\ - Fast, isolated
                   - Business logic
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/daniel-heydari-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

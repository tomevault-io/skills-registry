---
name: testing-standards
description: Test yazım standartları. TEST YAZARKEN veya TEST REVIEW yaparken otomatik uygula. Use when this capability is needed.
metadata:
  author: erhankaraarslan
---

# Testing Standards

## Test Piramidi

```
       /\        E2E (10%) - Critical user paths
      /  \
     /────\      Integration (20%) - API, service
    /      \
   /────────\    Unit (70%) - Business logic
```

## Test Yapısı (AAA)

```typescript
describe('UserService', () => {
  describe('createUser', () => {
    it('should create user with valid data', async () => {
      // Arrange
      const input = { email: 'test@example.com', name: 'Test' }
      
      // Act
      const result = await userService.createUser(input)
      
      // Assert
      expect(result.email).toBe(input.email)
    })
  })
})
```

## İsimlendirme

```typescript
// ✅ Doğru
it('should return user when valid id provided')
it('should throw NotFoundError when user does not exist')

// ❌ Yanlış
it('should work correctly')
it('test createUser')
```

## Best Practices

### Bağımsızlık
```typescript
beforeEach(() => {
  jest.clearAllMocks()
  // Fresh state her testte
})
```

### Edge Cases
```typescript
describe('calculateDiscount', () => {
  it('should apply discount at exactly 100', () => {...})
  it('should not apply below 100', () => {...})
  it('should throw for negative', () => {...})
  it('should handle null', () => {...})
})
```

### Mocking
```typescript
// ✅ External dependency mock
const mockEmailService = {
  send: jest.fn().mockResolvedValue(true)
}

// ❌ Internal helper mocklamaktan kaçın
```

## API Testing

```typescript
describe('POST /api/users', () => {
  it('should create user (201)', async () => {
    const response = await request(app)
      .post('/api/users')
      .send({ email: 'test@test.com', name: 'Test' })
      .expect(201)
    
    expect(response.body.success).toBe(true)
  })

  it('should return 400 for invalid email', async () => {
    await request(app)
      .post('/api/users')
      .send({ email: 'invalid' })
      .expect(400)
  })
})
```

## E2E Testing

```typescript
test('user registration flow', async ({ page }) => {
  await page.goto('/register')
  await page.fill('[data-testid="email"]', 'test@example.com')
  await page.click('[data-testid="submit"]')
  await expect(page).toHaveURL('/dashboard')
})
```

## Coverage Hedefleri

| Tür | Minimum | İdeal |
|-----|---------|-------|
| Unit | 70% | 85% |
| Integration | Kritik path | 80% |

## Checklist

- [ ] Tek bir şeyi test ediyor
- [ ] Bağımsız çalışıyor
- [ ] Açıklayıcı ismi var
- [ ] AAA pattern
- [ ] Edge case'ler

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erhankaraarslan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

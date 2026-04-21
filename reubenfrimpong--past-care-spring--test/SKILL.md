---
name: test
description: Run tests or write tests following PastCare testing requirements Use when this capability is needed.
metadata:
  author: reubenfrimpong
---

# Testing Skill

Run or write tests following PastCare testing requirements.

## Command
$ARGUMENTS

## Running Tests

### Targeted Tests (DEFAULT — use this unless explicitly asked for full suite)
Always prefer running only the tests related to the changed code. This is faster and avoids pre-existing failures in unrelated tests blocking your work.

```bash
# Backend - specific test class(es) related to the change
./mvnw test -Dtest=SpecificTestClass

# Backend - multiple test classes (comma-separated)
./mvnw test -Dtest=TestClassA,TestClassB

# Backend - by pattern (e.g. all Fellowship-related tests)
./mvnw test -Dtest="*Fellowship*"

# Frontend - specific E2E test
npx playwright test critical-path-01

# Frontend - E2E with UI
npx playwright test --ui
```

### Full Test Suite (only when explicitly requested or for final Definition of Done)
```bash
# Backend
./mvnw compile
./mvnw test

# Frontend (from /home/reuben/Documents/workspace/past-care-spring-frontend)
ng build --configuration=production
ng test --watch=false --browsers=ChromeHeadless
npx playwright test
```

## Writing Tests Requirements

### Role-Based Testing (MANDATORY)
Every test MUST cover ALL 7 user roles:
1. SUPERADMIN - Platform-level access
2. ADMIN - Church-level full access
3. PASTOR - Pastoral care and member oversight
4. TREASURER - Financial operations
5. MEMBER_MANAGER - Member data management
6. FELLOWSHIP_LEADER - Fellowship-scoped access
7. MEMBER - Limited personal access

Tests MUST assert:
- What each role CAN see/do
- What each role CANNOT see/do

### Tenant Isolation Tests (MANDATORY for services)
```java
@Test
void getAllVisitors_shouldOnlyReturnCurrentChurchVisitors() {
    // Given: Two churches with visitors
    Church church1 = createChurch("Church A");
    Church church2 = createChurch("Church B");
    Visitor visitor1 = createVisitor(church1);
    Visitor visitor2 = createVisitor(church2);

    // When: Church A admin calls getAllVisitors()
    authenticateAs(church1AdminUser);
    List<VisitorResponse> visitors = visitorService.getAllVisitors();

    // Then: Should only see Church A visitors
    assertThat(visitors).hasSize(1);
    assertThat(visitors.get(0).getId()).isEqualTo(visitor1.getId());
    assertThat(visitors).noneMatch(v -> v.getId().equals(visitor2.getId()));
}
```

### Test Locations
- Integration tests: `src/test/java/com/reuben/pastcare_spring/integration/`
- Security tests: `src/test/java/com/reuben/pastcare_spring/security/`
- E2E tests: `/home/reuben/Documents/workspace/past-care-spring-frontend/e2e/`

## Test Patterns

### Backend Integration Test
```java
@SpringBootTest
@AutoConfigureMockMvc
@Transactional
class FeatureIntegrationTest {
    @Autowired MockMvc mockMvc;

    @Test
    void endpoint_withAdminRole_shouldSucceed() {
        // Given
        authenticateAs(adminUser);

        // When
        var result = mockMvc.perform(get("/api/endpoint"))
            .andExpect(status().isOk());

        // Then
        // assertions
    }

    @Test
    void endpoint_withMemberRole_shouldBeForbidden() {
        authenticateAs(memberUser);
        mockMvc.perform(get("/api/endpoint"))
            .andExpect(status().isForbidden());
    }
}
```

### E2E Test Pattern
```typescript
test.describe('Feature Name', () => {
  test('admin can access feature', async ({ page }) => {
    await loginAs(page, 'admin');
    await page.goto('/feature');
    await expect(page.getByRole('heading')).toContainText('Feature');
  });

  test('member cannot access admin feature', async ({ page }) => {
    await loginAs(page, 'member');
    await page.goto('/admin-feature');
    await expect(page).toHaveURL('/unauthorized');
  });
});
```

## Verification Checklist

- [ ] All 7 roles tested
- [ ] Positive assertions (what role CAN do)
- [ ] Negative assertions (what role CANNOT do)
- [ ] Tenant isolation verified (if applicable)
- [ ] Edge cases covered
- [ ] Error scenarios tested

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/reubenfrimpong) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

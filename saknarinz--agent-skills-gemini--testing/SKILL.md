---
name: testing
description: เขียน Tests ทุกประเภทอย่างมีประสิทธิภาพ Use when this capability is needed.
metadata:
  author: saknarinz
---

# Testing Skill

## Overview

Skill สำหรับเขียน tests ที่มีคุณภาพ ครอบคลุม และ maintainable

## Test Pyramid

```
        /\
       /  \      E2E Tests (น้อย)
      /----\
     /      \    Integration Tests (ปานกลาง)
    /--------\
   /          \  Unit Tests (มาก)
  /------------\
```

- **Unit Tests** (70%) - ทดสอบ functions/components แยก
- **Integration Tests** (20%) - ทดสอบการทำงานร่วมกัน
- **E2E Tests** (10%) - ทดสอบ user flows ทั้งหมด

---

## Testing by Tech Stack

### Frontend Testing

#### React (Vitest + Testing Library)

```typescript
import { render, screen, fireEvent } from '@testing-library/react';
import { describe, it, expect, vi } from 'vitest';
import { Button } from './Button';

describe('Button', () => {
  it('renders with correct text', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });

  it('calls onClick when clicked', () => {
    const handleClick = vi.fn();
    render(<Button onClick={handleClick}>Click</Button>);
    fireEvent.click(screen.getByText('Click'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
});
```

#### Angular (Jasmine + TestBed)

```typescript
import { ComponentFixture, TestBed } from "@angular/core/testing";
import { ButtonComponent } from "./button.component";

describe("ButtonComponent", () => {
  let component: ButtonComponent;
  let fixture: ComponentFixture<ButtonComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [ButtonComponent],
    }).compileComponents();

    fixture = TestBed.createComponent(ButtonComponent);
    component = fixture.componentInstance;
  });

  it("should create", () => {
    expect(component).toBeTruthy();
  });
});
```

#### Vue (Vitest + Vue Test Utils)

```typescript
import { mount } from "@vue/test-utils";
import { describe, it, expect } from "vitest";
import Button from "./Button.vue";

describe("Button", () => {
  it("renders slot content", () => {
    const wrapper = mount(Button, {
      slots: { default: "Click me" },
    });
    expect(wrapper.text()).toContain("Click me");
  });

  it("emits click event", async () => {
    const wrapper = mount(Button);
    await wrapper.trigger("click");
    expect(wrapper.emitted("click")).toBeTruthy();
  });
});
```

---

### Backend Testing

#### Spring Boot (JUnit 5 + Mockito)

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    private UserRepository userRepository;

    @InjectMocks
    private UserService userService;

    @Test
    void findById_WhenUserExists_ReturnsUser() {
        // Arrange
        User user = new User(1L, "John");
        when(userRepository.findById(1L)).thenReturn(Optional.of(user));

        // Act
        User result = userService.findById(1L);

        // Assert
        assertThat(result.getName()).isEqualTo("John");
    }

    @Test
    void findById_WhenUserNotExists_ThrowsException() {
        when(userRepository.findById(1L)).thenReturn(Optional.empty());

        assertThrows(UserNotFoundException.class,
            () -> userService.findById(1L));
    }
}
```

#### Node.js (Jest)

```typescript
import { UserService } from "./user.service";
import { UserRepository } from "./user.repository";

jest.mock("./user.repository");

describe("UserService", () => {
  let userService: UserService;
  let mockRepository: jest.Mocked<UserRepository>;

  beforeEach(() => {
    mockRepository = new UserRepository() as jest.Mocked<UserRepository>;
    userService = new UserService(mockRepository);
  });

  describe("findById", () => {
    it("should return user when found", async () => {
      const mockUser = { id: 1, name: "John" };
      mockRepository.findById.mockResolvedValue(mockUser);

      const result = await userService.findById(1);

      expect(result).toEqual(mockUser);
    });
  });
});
```

#### Python (pytest)

```python
import pytest
from unittest.mock import Mock, patch
from app.services.user_service import UserService

class TestUserService:
    @pytest.fixture
    def user_service(self):
        mock_repo = Mock()
        return UserService(mock_repo), mock_repo

    def test_find_by_id_returns_user(self, user_service):
        service, mock_repo = user_service
        mock_repo.find_by_id.return_value = {"id": 1, "name": "John"}

        result = service.find_by_id(1)

        assert result["name"] == "John"

    def test_find_by_id_raises_when_not_found(self, user_service):
        service, mock_repo = user_service
        mock_repo.find_by_id.return_value = None

        with pytest.raises(UserNotFoundException):
            service.find_by_id(1)
```

---

### E2E Testing

#### Playwright

```typescript
import { test, expect } from "@playwright/test";

test.describe("Login Flow", () => {
  test("successful login redirects to dashboard", async ({ page }) => {
    await page.goto("/login");

    await page.fill('[data-testid="email"]', "user@example.com");
    await page.fill('[data-testid="password"]', "password123");
    await page.click('[data-testid="submit"]');

    await expect(page).toHaveURL("/dashboard");
    await expect(page.locator("h1")).toContainText("Welcome");
  });
});
```

#### Cypress

```typescript
describe("Login Flow", () => {
  it("successful login redirects to dashboard", () => {
    cy.visit("/login");

    cy.get('[data-testid="email"]').type("user@example.com");
    cy.get('[data-testid="password"]').type("password123");
    cy.get('[data-testid="submit"]').click();

    cy.url().should("include", "/dashboard");
    cy.contains("h1", "Welcome");
  });
});
```

---

## Testing Best Practices

### Naming Convention

```
test_[methodName]_[scenario]_[expectedBehavior]

// Examples:
test_findById_whenUserExists_returnsUser()
test_createUser_withInvalidEmail_throwsValidationError()
```

### AAA Pattern

```typescript
it("should do something", () => {
  // Arrange - ตั้งค่าข้อมูลและ dependencies
  const input = "test";

  // Act - เรียก function ที่ต้องการทดสอบ
  const result = myFunction(input);

  // Assert - ตรวจสอบผลลัพธ์
  expect(result).toBe("expected");
});
```

### Test Isolation

- แต่ละ test ต้องเป็นอิสระ
- ใช้ beforeEach/afterEach สำหรับ setup/cleanup
- อย่าให้ tests depend on ลำดับการ run

---

## Testing Checklist

- [ ] เขียน Unit tests สำหรับ business logic
- [ ] Mock external dependencies
- [ ] ทดสอบ edge cases และ error cases
- [ ] ใช้ meaningful test names
- [ ] ตาม AAA pattern
- [ ] ไม่มี test dependencies
- [ ] Coverage อย่างน้อย 80%
- [ ] Integration tests สำหรับ API endpoints
- [ ] E2E tests สำหรับ critical user flows

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/saknarinz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

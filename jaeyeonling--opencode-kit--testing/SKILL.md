---
name: testing
description: 테스트 작성 전략, TDD, 테스트 종류별 가이드 Use when this capability is needed.
metadata:
  author: jaeyeonling
---

# Testing Guide

## 테스트 피라미드

```text
        /\
       /  \      E2E Tests (few, slow, expensive)
      /----\
     /      \    Integration Tests
    /--------\
   /          \  Unit Tests (many, fast, cheap)
  /------------\
```

## 테스트 종류

### Unit Tests

개별 함수/메서드의 독립적인 테스트

```text
# Good: single function test
test "calculateTotal returns sum of items":
    items = [{price: 100}, {price: 200}]
    assert calculateTotal(items) == 300

# Good: edge case test
test "calculateTotal returns 0 for empty array":
    assert calculateTotal([]) == 0
```

### Integration Tests

여러 컴포넌트의 상호작용 테스트

```text
test "user can add item to cart":
    user = createUser()
    product = createProduct()
    
    addToCart(user.id, product.id)
    
    cart = getCart(user.id)
    assert cart.items contains product
```

### E2E Tests

전체 시스템의 사용자 시나리오 테스트

```text
test "user can complete checkout flow":
    goto("/products")
    click("[data-testid=add-to-cart]")
    click("[data-testid=checkout]")
    fill("input[name=email]", "test@example.com")
    click("[data-testid=submit]")
    
    assert current_url contains "/confirmation"
```

## TDD (Test-Driven Development)

### Red-Green-Refactor 사이클

1. **Red**: 실패하는 테스트 먼저 작성
2. **Green**: 테스트를 통과하는 최소한의 코드 작성
3. **Refactor**: 코드 개선 (테스트는 계속 통과)

### TDD 예시

```text
# Step 1: Red - failing test
test "formatCurrency formats number as KRW":
    assert formatCurrency(1000) == "₩1,000"

# Step 2: Green - passing code
function formatCurrency(amount):
    return "₩" + formatWithCommas(amount)

# Step 3: Refactor - improve if needed
```

## 테스트 네이밍

### Given-When-Then 패턴

```text
test "given empty cart, when adding item, then cart has one item":
    # Given
    cart = new Cart()
    
    # When
    cart.addItem(product)
    
    # Then
    assert length(cart.items) == 1
```

### 좋은 테스트 이름

```text
# Good: clear about what is being tested
test "returns null when user not found"
test "throws error for invalid email format"
test "filters out inactive users"

# Bad: vague or unclear
test "test user"
test "should work"
test "handles edge case"
```

## Mock과 Stub

### 언제 사용하는가?

- **Mock**: 외부 의존성 격리 (API, DB)
- **Stub**: 특정 값 반환 설정
- **Spy**: 함수 호출 추적

```text
# Mock example
mockFetch = mock(fetch)
mockFetch.returns({data: "test"})

# Stub example
stub(Date, "now").returns(1234567890)

# Spy example
spy = spyOn(logger, "error")
# ... execute code
assert spy.wasCalledWith("Error message")
```

## 테스트 체크리스트

### 단위 테스트

- [ ] 정상 케이스 테스트
- [ ] 엣지 케이스 테스트 (빈 값, null, 경계값)
- [ ] 에러 케이스 테스트
- [ ] 각 테스트가 독립적인가?

### 통합 테스트

- [ ] 주요 사용자 시나리오 커버
- [ ] 에러 처리 흐름 테스트
- [ ] 데이터 정합성 확인

### 일반 원칙

- [ ] 테스트가 빠르게 실행되는가?
- [ ] 테스트가 안정적인가? (flaky하지 않은가)
- [ ] 테스트가 이해하기 쉬운가?
- [ ] 실패 시 원인 파악이 쉬운가?

## 커버리지

### 커버리지 목표

- 새 코드: 80% 이상
- 핵심 비즈니스 로직: 90% 이상
- 유틸리티 함수: 100%

### 커버리지의 한계

- 높은 커버리지 ≠ 좋은 테스트
- 의미 있는 assertion이 중요
- 가치 있는 테스트에 집중

## 관련 스킬

- `code-quality`: 클린 코드 원칙
- `debugging`: 테스트 실패 분석

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jaeyeonling) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

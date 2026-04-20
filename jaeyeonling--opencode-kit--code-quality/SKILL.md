---
name: code-quality
description: 코드 품질 가이드, 클린 코드 원칙, 리팩토링 패턴 Use when this capability is needed.
metadata:
  author: jaeyeonling
---

# Code Quality Guide

## 클린 코드 원칙

### 1. 의미 있는 이름

```text
# Bad
d = get_current_date()
arr = filter(u -> u.a > 18, users)

# Good
currentDate = get_current_date()
adultUsers = filter(user -> user.age > 18, users)
```

### 2. 함수는 작게, 한 가지 일만

```text
# Bad: function doing multiple things
function processUser(user):
    # validation
    if not user.email:
        throw Error("Email required")
    # data transformation
    user.email = lowercase(user.email)
    # DB save
    db.users.insert(user)
    # send email
    sendWelcomeEmail(user)

# Good: single responsibility
function createUser(userData):
    validatedData = validateUserData(userData)
    normalizedData = normalizeUserData(validatedData)
    user = saveUser(normalizedData)
    sendWelcomeEmail(user)
    return user
```

### 3. 주석보다 코드로 표현

```text
# Bad: explaining with comment
# check if adult
if user.age >= 18:
    ...

# Good: explain with function name
if isAdult(user):
    ...

function isAdult(user):
    ADULT_AGE = 18
    return user.age >= ADULT_AGE
```

### 4. 에러 처리

```text
# Bad: ignoring error
try:
    doSomething()
catch error:
    pass  # do nothing

# Good: proper error handling
try:
    doSomething()
catch error:
    logger.error("Operation failed", error, context)
    throw ApplicationError("Failed to complete operation", error)
```

## SOLID 원칙

### Single Responsibility (단일 책임)

```text
# Bad: multiple responsibilities
class UserService:
    createUser()
    sendEmail()
    generateReport()

# Good: single responsibility
class UserService:
    createUser()

class EmailService:
    sendEmail()

class ReportService:
    generateReport()
```

### Open/Closed (개방/폐쇄)

```text
# Bad: open for modification
function calculateArea(shape):
    if shape.type == "circle":
        return PI * shape.radius ^ 2
    else if shape.type == "rectangle":
        return shape.width * shape.height
    # need to modify function for new shapes

# Good: open for extension
interface Shape:
    calculateArea()

class Circle implements Shape:
    calculateArea():
        return PI * self.radius ^ 2

class Rectangle implements Shape:
    calculateArea():
        return self.width * self.height
```

## 리팩토링 패턴

### Extract Method

```text
# Before
function printReport(user):
    print("=" * 40)
    print("Name: " + user.name)
    print("Email: " + user.email)
    print("=" * 40)

# After
function printReport(user):
    printSeparator()
    printUserDetails(user)
    printSeparator()

function printSeparator():
    print("=" * 40)

function printUserDetails(user):
    print("Name: " + user.name)
    print("Email: " + user.email)
```

### Replace Conditional with Polymorphism

```text
# Before
function getSpeed(vehicle):
    switch vehicle.type:
        case "car": return vehicle.baseSpeed * 1.2
        case "bike": return vehicle.baseSpeed * 0.8
        default: return vehicle.baseSpeed

# After
class Vehicle:
    getSpeed(): return self.baseSpeed

class Car extends Vehicle:
    getSpeed(): return self.baseSpeed * 1.2

class Bike extends Vehicle:
    getSpeed(): return self.baseSpeed * 0.8
```

### Guard Clause

```text
# Before: nested conditionals
function processOrder(order):
    if order:
        if length(order.items) > 0:
            if order.status == "pending":
                # process logic

# After: Guard Clause
function processOrder(order):
    if not order: return
    if length(order.items) == 0: return
    if order.status != "pending": return
    
    # process logic
```

## 코드 스멜 (Code Smells)

### 탐지해야 할 패턴

| 스멜 | 증상 | 해결책 |
|------|------|--------|
| Long Method | 50줄 이상의 함수 | Extract Method |
| Large Class | 너무 많은 책임 | Extract Class |
| Long Parameter List | 3개 이상 파라미터 | Parameter Object |
| Duplicate Code | 중복된 코드 블록 | Extract Method/Class |
| Feature Envy | 다른 클래스 데이터에 과도한 접근 | Move Method |
| Magic Numbers | 의미 불명의 숫자 | Named Constant |

### 예시

```text
# Magic Numbers (Bad)
if user.age >= 18 and length(items) <= 10:
    ...

# Named Constants (Good)
ADULT_AGE = 18
MAX_CART_ITEMS = 10

if user.age >= ADULT_AGE and length(items) <= MAX_CART_ITEMS:
    ...
```

## 코드 리뷰 체크리스트

- [ ] 코드가 읽기 쉬운가?
- [ ] 함수가 한 가지 일만 하는가?
- [ ] 이름이 의도를 명확히 드러내는가?
- [ ] 중복 코드가 있는가?
- [ ] 에러 처리가 적절한가?
- [ ] 테스트가 있는가?
- [ ] 성능 이슈가 있는가?
- [ ] 보안 취약점이 있는가?

## 관련 스킬

- `testing`: 테스트 작성 전략
- `debugging`: 에러 추적 및 디버깅

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jaeyeonling) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

---
name: clean-code
description: 코드 작성/수정, 리팩토링 시 자동 활성화. Robert C. Martin Clean Code 원칙(명명, 함수 크기, 주석, 포맷팅, DRY, SRP) 검증 및 제안. 함수 20줄 초과, 인자 3개 초과, 중복 코드, 불명확한 변수명, 과도한 주석 발견 시 가독성 개선 방향 제시. Use when this capability is needed.
metadata:
  author: guny524
---

# Clean Code 원칙

You are a Clean Code guardian and senior software craftsmanship mentor.
Your goal is to review, refactor, or generate code that strictly follows the Clean Code principles from Robert C. Martin.

## Primary Goal

Code must be easy to read and change. Clarity over cleverness.

코드는 작성되는 횟수보다 읽히는 횟수가 훨씬 많습니다. 유지보수 비용 최소화를 위해 코드가 명확하고 의도를 명확히 전달해야 합니다.

---

## 13가지 Clean Code 원칙

### 1. Meaningful Names (의미 있는 이름)

원칙: 의도를 드러내는 이름 사용

규칙:
- 왜 존재하는지, 무엇을 하는지, 어떻게 사용되는지 알려줘야 함
- 무의미한 접두사/접미사, 발음 어려운 단어, 중복 정보, 오해 소지 약어 금지

예시:

```typescript
// ❌ 나쁜 예
const d = 7; // elapsed time in days
let list1: User[];
function getData() {}

// ✅ 좋은 예
const elapsedTimeInDays = 7;
let activeUsers: User[];
function getUsersByRole(role: string) {}
```

---

### 2. Small Functions (작은 함수)

원칙: 함수는 짧고, 한 가지만 하고, 그것을 잘 해야 함

규칙:
- 20줄 이내, 대부분 10줄 이내
- 한 가지 일만 수행 (Single Responsibility)
- Side effect 금지
- 인자 최소화 (이상적: 0~2개, 최대: 3개)

예시:

```typescript
// ❌ 나쁜 예 (너무 긴 함수, 여러 일 수행)
function processUserData(user: User) {
  if (!user.email) throw new Error('Email required');
  if (!user.name) throw new Error('Name required');
  const formattedName = user.name.trim().toLowerCase();
  db.users.save({ ...user, name: formattedName });
  sendEmail(user.email, 'Welcome!');
  logger.info(`User ${user.id} processed`);
}

// ✅ 좋은 예 (작은 함수, 각각 한 가지 일)
function validateUser(user: User): void {
  if (!user.email) throw new Error('Email required');
  if (!user.name) throw new Error('Name required');
}

function formatUserName(name: string): string {
  return name.trim().toLowerCase();
}

function processUserData(user: User): void {
  validateUser(user);
  const formattedUser = { ...user, name: formatUserName(user.name) };
  await saveUser(formattedUser);
  sendWelcomeEmail(user.email);
}
```

인자 많을 때:

```typescript
// ❌ 나쁜 예
function createUser(name: string, email: string, age: number, city: string, country: string) {}

// ✅ 좋은 예 (객체로 묶기)
interface CreateUserParams {
  name: string;
  email: string;
  age: number;
  city: string;
  country: string;
}

function createUser(params: CreateUserParams) {}
```

---

### 3. Comments (주석)

원칙: 필수적일 때만 사용. 코드 자체가 설명되도록 (self-explanatory)

규칙:
- What이 아닌 Why 설명
- 명백한 것에 주석 달지 않기
- 오해의 소지 있는 주석, 오래된 주석 금지

예시:

```typescript
// ❌ 나쁜 예 (명백한 것 설명)
// 사용자 이름을 가져온다
const name = user.name;

// ✅ 좋은 예 (Why 설명)
// Safari에서 Date parsing 버그 때문에 moment 사용
const date = moment(dateString);

// 관리자는 본인 팀의 데이터만 볼 수 있지만,
// 슈퍼관리자는 모든 팀 데이터를 볼 수 있음
if (user.role === 'SUPER_ADMIN' || team.id === user.teamId) {
  return data;
}
```

---

### 4. Formatting (포맷팅)

원칙: 일관된 들여쓰기, 간격, 코드 레이아웃으로 가독성 확보

규칙:
- 일관된 들여쓰기 (2 spaces or 4 spaces)
- 세로 간격으로 논리 그룹화
- 가로 길이 제한 (80~120자)
- Prettier/ESLint 사용

예시:

```typescript
// ❌ 나쁜 예
function calculatePrice(items:Item[]){const subtotal=items.reduce((sum,item)=>sum+item.price,0);const tax=subtotal*0.1;return subtotal+tax;}

// ✅ 좋은 예
function calculatePrice(items: Item[]): number {
  const subtotal = items.reduce((sum, item) => sum + item.price, 0);
  const tax = subtotal * 0.1;
  return subtotal + tax;
}
```

---

### 5. Objects vs Data Structures

원칙: 객체는 행동을 캡슐화, 데이터 구조는 데이터만 노출

규칙:
- Anemic domain model 피하기
- 객체에 행동(메서드) 포함

예시:

```typescript
// ❌ 나쁜 예 (Anemic model)
interface User {
  name: string;
  email: string;
}

function isValidEmail(user: User): boolean {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(user.email);
}

// ✅ 좋은 예 (행동 캡슐화)
class User {
  constructor(private name: string, private email: string) {}

  isValidEmail(): boolean {
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(this.email);
  }

  getDisplayName(): string {
    return this.name.trim();
  }
}
```

---

### 6. Error Handling (에러 처리)

원칙: 예외 사용, 에러 코드 금지. null 반환/전달 금지

규칙:
- 예외 사용 (not error codes)
- null 반환 금지 → Optional 또는 기본값

예시:

```typescript
// ❌ 나쁜 예 (error code)
function getUser(id: string): { user: User | null; error: string | null } {
  if (!id) return { user: null, error: 'ID required' };
}

// ✅ 좋은 예 (예외 사용)
function getUser(id: string): User {
  if (!id) throw new Error('ID required');
}

// ✅ 좋은 예 (Optional)
function findUserByEmail(email: string): User | undefined {
  return users.find(u => u.email === email);
}
```

---

### 7. Boundaries (경계)

원칙: 외부 라이브러리/API를 어댑터/래퍼로 격리하여 내부 코드 보호

예시:

```typescript
// ❌ 나쁜 예 (외부 라이브러리 직접 사용)
import axios from 'axios';
async function getUsers() {
  const response = await axios.get('/api/users');
  return response.data;
}

// ✅ 좋은 예 (어댑터로 격리)
// httpClient.ts
export const httpClient = {
  async get<T>(url: string): Promise<T> {
    const response = await axios.get(url);
    return response.data;
  },
};

// userService.ts
async function getUsers() {
  return httpClient.get<User[]>('/api/users');
}
```

---

### 8. Unit Tests

원칙: FIRST 원칙 (Fast, Independent, Repeatable, Self-validating, Timely)

규칙:
- 빠르고 (Fast)
- 독립적이고 (Independent)
- 반복 가능하고 (Repeatable)
- 자가 검증되고 (Self-validating)
- 적시에 작성 (Timely)

예시:

```typescript
describe('calculatePrice', () => {
  it('should calculate price with tax', () => {
    const items = [{ price: 100 }, { price: 200 }];
    expect(calculatePrice(items)).toBe(330);
  });

  it('should return 0 for empty items', () => {
    expect(calculatePrice([])).toBe(0);
  });
});
```

---

### 9. Classes

원칙: 작고, 단일 책임, 높은 응집도, 낮은 결합도

규칙:
- Small, Single Responsibility Principle
- High cohesion, Low coupling

예시:

```typescript
// ❌ 나쁜 예 (너무 많은 책임)
class User {
  validateEmail() {}
  saveToDatabase() {}
  sendEmail() {}
  generateReport() {}
}

// ✅ 좋은 예 (단일 책임)
class User {
  constructor(private email: string, private name: string) {}
  isValid(): boolean {
    return this.email.includes('@');
  }
}

class UserRepository {
  save(user: User): Promise<void> {}
}

class EmailService {
  send(email: string, message: string): void {}
}
```

---

### 10. System Design

원칙: SRP, OCP, DIP 적용. 중복 제거, 의도 명확히, 컴포넌트 최소화

규칙:
- Single Responsibility Principle
- Open-Closed Principle
- Dependency Inversion Principle
- DRY (Don't Repeat Yourself)

---

### 11. Concurrency (동시성)

원칙: 공유 가변 상태 최소화, 불변성 선호, race condition/deadlock 방지

예시:

```typescript
// ❌ 나쁜 예 (공유 가변 상태)
let counter = 0;
async function incrementCounter() {
  counter++;
}

// ✅ 좋은 예 (불변성)
async function incrementCounter(counter: number): Promise<number> {
  return counter + 1;
}
```

---

### 12. Refactoring (리팩토링)

원칙: 지속적으로 개선. Code smell 제거

Code Smells:
- 긴 메서드, 큰 클래스, 중복
- 나쁜 이름, 임시 필드, 과도한 주석

리팩토링은 마인드셋: 정기적으로 코드를 재방문하고 개선

---

### 13. Clarity over Cleverness

원칙: 똑똑한 코드보다 명확한 코드

```typescript
// ❌ 나쁜 예 (clever but unclear)
const result = arr.reduce((a, b) => [...a, ...b.items], [])
  .filter(x => x.active)
  .map(x => ({ ...x, price: x.price * 1.1 }));

// ✅ 좋은 예 (clear)
const allItems = arr.flatMap(order => order.items);
const activeItems = allItems.filter(item => item.active);
const itemsWithTax = activeItems.map(item => ({
  ...item,
  price: item.price * 1.1,
}));
```

---

## 코드 리뷰 시 체크리스트

빠른 체크 (3가지):
1. **의도가 명확한가?** (명명, 주석)
2. **간결한가?** (함수 크기, DRY)
3. **안전한가?** (에러 처리, Side effect)

상세 체크리스트: [sources/checklist.md](~/llms/skills/clean-code/sources/checklist.md)

---

## 위반 발견 시

Whenever you review or write code:
1. 명시적으로 위반 지적: 어떤 원칙을 위반했는지
2. 이유 설명: 왜 이것이 원칙을 위반하는지
3. Clean한 대안 제시: 리팩토링된 코드 제안

예시:

```
❌ 위반: Meaningful Names 원칙 위반
이유: 변수명 `d`가 의도를 드러내지 않음
✅ 제안: `const elapsedTimeInDays = 7;`로 변경
```

Take a deep breath and let's work this out in a step-by-step way to ensure we have the cleanest possible solution.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guny524) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

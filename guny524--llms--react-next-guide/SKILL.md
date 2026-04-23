---
name: react-next-guide
description: Next.js + React 개발 시 자동 활성화. app router 사용, server/client component 구분, shadcn UI, prisma 사용 원칙, use* hook 최소화, any 타입 금지 등 Next.js 13+ 개발 원칙 제공. .tsx, .ts 파일 작성 또는 Next.js 관련 질문 시 자동 트리거. Use when this capability is needed.
metadata:
  author: guny524
---

# Next.js + React 개발 가이드
Next.js 13+ (app router) 및 React 개발 시 따라야 할 원칙들

## 1. 절대 금지

### 1-1. any 타입 사용 금지
어떤 상황이 와도 절대 `any`는 사용하지 말 것

이유:
- 상식적으로 생각해보자. Next.js로 개발하는 경우 간단한 웹페이지에 이미 data domain이 정해져 있는 경우가 대부분
- 특수한 경우를 제외하고 이미 props에 어떤 항목이 들어갈지 specific하게 정해져있고 명확한 경우가 대부분
- `any`나 `unknown`을 쓰는 경우는 거의 없다

대신:
```typescript
// ❌ 나쁜 예
interface Props {
  data: any;
  config: any;
}

// ✅ 좋은 예
interface Props {
  data: {
    id: string;
    name: string;
    email: string;
  };
  config: {
    theme: 'light' | 'dark';
    locale: string;
  };
}
```

### 1-2. 타입 캐스팅(as) 절대 금지
원칙: `as` 타입 단언(type assertion)을 사용하지 말 것. **특히 `as unknown as Type` 이중 캐스팅은 절대 금지**

이유:
- `as`는 TypeScript 컴파일러에게 거짓말을 하는 것
- 컴파일 타임에는 문제없지만 런타임에 타입 불일치로 크래시 발생
- Type guard 같은 것도 복잡하고 난잡함

**근본 해결책: Props/DTO/Interface를 처음부터 제대로 정의하면 캐스팅이 필요 없다**

#### 상황 1: 제네릭 컴포넌트에서 특정 타입 필드 접근
```typescript
// ❌ 나쁜 예 (제네릭 + 캐스팅)
interface TableProps<T> {
  items: T[];
}

function Table<T>({ items }: TableProps<T>) {
  return items.map(item => {
    const user = item as unknown as User; // 절대 금지!
    return <div>{user.name}</div>;
  });
}

// ✅ 좋은 예 1: 제네릭 제약으로 필요한 필드 명시
interface HasName {
  name: string;
}

interface TableProps<T extends HasName> {
  items: T[];
}

function Table<T extends HasName>({ items }: TableProps<T>) {
  return items.map(item => <div>{item.name}</div>); // 타입 안전
}

// ✅ 좋은 예 2: 제네릭 쓰지 말고 구체적 타입 선언
interface UserTableProps {
  items: User[];
}

function UserTable({ items }: UserTableProps) {
  return items.map(item => <div>{item.name}</div>);
}
```

#### 상황 2: Union Type (여러 타입 중 하나)
```typescript
// ❌ 나쁜 예 (타입 체크 없이 캐스팅)
function render(item: User | Post) {
  const user = item as User; // 위험!
  return user.name;
}

// ✅ 좋은 예 1: Discriminated Union (타입 태그)
type Item =
  | { type: 'user'; name: string; email: string }
  | { type: 'post'; title: string; content: string };

function renderItem(item: Item) {
  switch (item.type) {
    case 'user':
      return item.name; // 자동 타입 좁혀짐
    case 'post':
      return item.title;
  }
}

// ✅ 좋은 예 2: 'in' 연산자로 타입 좁히기
function render(item: User | Post) {
  if ('name' in item) {
    return item.name; // User
  }
  return item.title; // Post
}
```

#### 상황 3: GraphQL/API 응답
```typescript
// ❌ 나쁜 예 (수동 캐스팅)
const response = await fetch('/api/users');
const users = await response.json() as User[]; // 위험!

// ✅ 좋은 예 1: GraphQL Codegen 사용
import { useGetUsersQuery } from '@/graphql/generated';

function UsersPage() {
  const { data } = useGetUsersQuery(); // 타입 자동 생성
  return <ul>{data?.users.map(u => <li>{u.name}</li>)}</ul>;
}

// ✅ 좋은 예 2: DTO 명시적 선언 + 런타임 검증
interface UserDTO {
  id: string;
  name: string;
  email: string;
}

function parseUsers(data: unknown): UserDTO[] {
  // Zod, Yup 같은 라이브러리로 런타임 검증
  return UserSchema.parse(data);
}
```

#### 상황 4: 외부 라이브러리 타입이 없을 때
```typescript
// ❌ 나쁜 예
const result = externalLib.doSomething() as MyType;

// ✅ 좋은 예: .d.ts 파일로 타입 선언
// types/external-lib.d.ts
declare module 'external-lib' {
  export function doSomething(): MyType;
}

// 사용
const result = externalLib.doSomething(); // 타입 안전
```

### 1-3. Page Router 금지
Page router 쓰지 말고 app router 기반으로 작성

이유:
- Next.js 13+의 표준
- Server Components 지원
- 더 나은 성능과 타입 안정성

## 2. 프로젝트 구조

### app router 구조
```
app/
├── page.tsx              # 홈 페이지
├── layout.tsx            # 레이아웃
├── api/
│   └── route.ts          # API 엔드포인트
├── (auth)/
│   ├── login/page.tsx
│   └── signup/page.tsx
└── dashboard/
    └── page.tsx
```

## 3. Next.js 개발 원칙

### 3-1. Lint 실행
코드를 수정하면 `next lint`를 돌려볼 것

### 3-2. Next.js 기능 우선 사용
Next나 React를 건드릴 일이 있으면 최대한 Next.js에서 제공하는 기능을 사용

예시:
- 이미지: `<Image>` from `next/image`
- 링크: `<Link>` from `next/link`
- 폰트: `next/font`
- 메타데이터: `metadata` export

### 3-3. API vs Server Functions vs Server Actions
`app/api` 밑에 GET, POST 같은 함수들이 적절하게 설계된 것인지 고민할 것

고민 사항:
- Server side function으로 대체될 수 없는 것인지?
- Form 제출 시 server side action으로 대체될 수 없는 것인지?

우선순위:
1. Server Component에서 직접 DB 호출 (가장 간단)
2. Server Actions (form 제출, mutation)
3. API Routes (외부 API, webhook, third-party integration)

예시:

```typescript
// ✅ 1. Server Component에서 직접 DB 호출 (가장 선호)
// app/users/page.tsx
import { prisma } from '@/lib/prisma';

export default async function UsersPage() {
  const users = await prisma.user.findMany();
  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}

// ✅ 2. Server Actions (mutation)
// app/users/actions.ts
'use server';
import { prisma } from '@/lib/prisma';

export async function createUser(formData: FormData) {
  const name = formData.get('name') as string;
  await prisma.user.create({ data: { name } });
}

// app/users/new/page.tsx
import { createUser } from '../actions';

export default function NewUserPage() {
  return (
    <form action={createUser}>
      <input name="name" />
      <button type="submit">Create</button>
    </form>
  );
}

// ❌ 3. API Routes (불필요한 경우)
// app/api/users/route.ts
// 이건 외부에서 호출할 일이 없으면 불필요
export async function GET() {
  const users = await prisma.user.findMany();
  return Response.json(users);
}
```

## 4. Prisma 사용 원칙

### 4-1. Server Side에서만 호출
원칙: 모든 DB 접근은 prisma client를 server side에서 호출하도록

### 4-2. Singleton 사용
원칙: Prisma를 사용하는 경우 `lib/prisma.ts`의 singleton 객체를 import하여 사용할 것

```typescript
// lib/prisma.ts
import { PrismaClient } from '@prisma/client';

const globalForPrisma = globalThis as unknown as {
  prisma: PrismaClient | undefined;
};

export const prisma = globalForPrisma.prisma ?? new PrismaClient();

if (process.env.NODE_ENV !== 'production') globalForPrisma.prisma = prisma;
```

### 4-3. 직접 호출 원칙
원칙: 의미 없는 prisma query 함수를 미리 만들어놓지 말 것

이유:
- 호출이나 전후처리가 복잡해서 함수로 추상화가 필요한 경우가 아니면
- 필요할 때 page에서 `await prisma.<table_name>.findMany()` 같은 형식으로 page에서 직접 호출할 것

예시:

```typescript
// ❌ 나쁜 예 (불필요한 추상화)
// lib/queries/users.ts
export async function getUsers() {
  return await prisma.user.findMany();
}

// app/users/page.tsx
import { getUsers } from '@/lib/queries/users';
const users = await getUsers();

// ✅ 좋은 예 (직접 호출)
// app/users/page.tsx
import { prisma } from '@/lib/prisma';
const users = await prisma.user.findMany();


// ✅ 좋은 예 (복잡한 로직이 있을 때만 함수 추상화)
// lib/queries/users.ts
export async function getUsersWithPosts(userId: string) {
  // 복잡한 join, 필터링, 가공
  const user = await prisma.user.findUnique({
    where: { id: userId },
    include: {
      posts: {
        where: { published: true },
        orderBy: { createdAt: 'desc' },
        take: 10,
      },
    },
  });

  // 추가 가공
  return {
    ...user,
    postCount: user?.posts.length ?? 0,
  };
}
```

## 5. UI 컴포넌트 개발 가이드 (shadcn)

### 5-1. shadcn 사용
원칙: Component를 만들거나 button이던 뭐던 UI를 구성할 일이 있으면 shadcn을 사용

설치:
```bash
npx shadcn-ui@latest add button
npx shadcn-ui@latest add input
npx shadcn-ui@latest add dialog
```

위치: `components/ui/`

### 5-2. shadcn 파일 수정 금지
원칙: `components/ui/` 안의 shadcn 파일은 절대 수정하지 말 것

이유: 덮어쓰거나 조합할 일이 있으면 Wrap*, Composit*으로 시작하는 component를 `components/`에 만들어서 사용할 것

예시:

```typescript
// ❌ 나쁜 예 (shadcn 파일 직접 수정)
// components/ui/button.tsx
export function Button() {
  // 수정...
}

// ✅ 좋은 예 (Wrapper 생성)
// components/WrapButton.tsx
import { Button } from '@/components/ui/button';

export function WrapButton({ children, ...props }: ButtonProps) {
  return (
    <Button {...props} className="my-custom-class">
      {children}
    </Button>
  );
}

// ✅ 좋은 예 (Composite 생성)
// components/CompositLoginButton.tsx
import { Button } from '@/components/ui/button';
import { LogIn } from 'lucide-react';

export function CompositLoginButton() {
  return (
    <Button>
      <LogIn className="mr-2 h-4 w-4" />
      로그인
    </Button>
  );
}
```

### 5-3. ESLint 제외 설정
원칙: `components/ui/`는 install 후 수정하지 않으므로 ESLint에서 제외

이유:
- shadcn 파일은 외부에서 가져온 것이므로 프로젝트 lint 규칙 적용 불필요
- 업데이트 시 덮어쓰므로 lint 수정이 의미 없음

설정:

```.eslintignore
# shadcn UI components (auto-generated, do not modify)
components/ui/**
```

또는 `.eslintrc.json`:
```json
{
  "ignorePatterns": ["components/ui/**"]
}
```

## 6. React Hook 사용 원칙

### 6-1. Page에서 use* hook 최소화
원칙: 진짜진짜진짜 불가피한 경우 말고 page에서 `useState` 같은 `use*`로 시작하는 React client side hook을 사용하지 말 것

이유: Server Component 우선, Client Component 최소화

### 6-2. Component에서도 use* hook 최소화
원칙: Component에서도 웬만하면 `use*` hook을 사용하지 말 것

대신: 결국 사용자가 서버로 데이터를 제출해야 하는 거면 form에 server side action을 달아서 server side에서 db를 호출하여 상태가 저장되게 할 것

예시:

```typescript
// ❌ 나쁜 예 (Page에서 useState)
// app/todos/page.tsx
'use client';
import { useState } from 'react';

export default function TodosPage() {
  const [todos, setTodos] = useState([]);
  // ...
}

// ✅ 좋은 예 (Server Component + Server Action)
// app/todos/page.tsx
import { prisma } from '@/lib/prisma';
import { TodoForm } from './TodoForm';

export default async function TodosPage() {
  const todos = await prisma.todo.findMany();
  return (
    <div>
      <TodoForm />
      <ul>{todos.map(t => <li key={t.id}>{t.title}</li>)}</ul>
    </div>
  );
}

// app/todos/TodoForm.tsx
import { addTodo } from './actions';

export function TodoForm() {
  return (
    <form action={addTodo}>
      <input name="title" />
      <button type="submit">Add</button>
    </form>
  );
}

// app/todos/actions.ts
'use server';
import { prisma } from '@/lib/prisma';
import { revalidatePath } from 'next/cache';

export async function addTodo(formData: FormData) {
  const title = formData.get('title') as string;
  await prisma.todo.create({ data: { title } });
  revalidatePath('/todos');
}
```

### 6-3. 상태 관리 계층
원칙:
- 사용자가 제출하기 전까지는 component 단에서 `use*`로 상태를 관리한다
- 제출 후에는 server side action으로 DB에 저장

예시:

```typescript
// ✅ 좋은 예 (제출 전 client, 제출 후 server)
// components/TodoForm.tsx
'use client';
import { useState } from 'react';
import { addTodo } from './actions';

export function TodoForm() {
  const [title, setTitle] = useState('');

  return (
    <form action={async () => {
      await addTodo(title);
      setTitle(''); // 제출 후 초기화
    }}>
      <input
        value={title}
        onChange={(e) => setTitle(e.target.value)}
      />
      <button type="submit">Add</button>
    </form>
  );
}
```

### 6-4. Component 분리 원칙
원칙: `use*` hook을 page 단에서 사용하지 않기 위해 의미 없는 component 분리는 하지 말 것

고민: Component 분리 시 이 component가 진짜 재사용될 수 있게 분리가 되었는지 고민할 것

예시:

```typescript
// ❌ 나쁜 예 (의미 없는 분리)
// app/page.tsx
import { Counter } from './Counter';
export default function Page() {
  return <Counter />;
}

// Counter.tsx (한 곳에서만 사용)
'use client';
export function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}

// ✅ 좋은 예 (재사용 가능한 분리)
// components/Counter.tsx (여러 곳에서 사용)
'use client';
interface CounterProps {
  initialValue?: number;
  onCountChange?: (count: number) => void;
}

export function Counter({ initialValue = 0, onCountChange }: CounterProps) {
  const [count, setCount] = useState(initialValue);

  const handleClick = () => {
    const newCount = count + 1;
    setCount(newCount);
    onCountChange?.(newCount);
  };

  return <button onClick={handleClick}>{count}</button>;
}

// app/page1.tsx, app/page2.tsx에서 재사용
```

## 7. 'use server' / 'use client' 지시어

### 원칙
원칙: `'use server'`나 `'use client'`를 불가피한 경우 빼고는 명시하지 말고 static으로 build될 수 있게 할 것

이유:
- Next.js는 기본적으로 Server Component
- 명시적으로 'use client'가 없으면 Server Component로 처리
- Static rendering이 가능하면 성능이 더 좋음

'use client'가 필요한 경우:
- `useState`, `useEffect` 등 React hook 사용
- Event handlers (`onClick`, `onChange` 등)
- Browser APIs 사용 (`window`, `document`)
- Third-party libraries (대부분 client 전용)

'use server'가 필요한 경우:
- Server Actions (mutation)
- 별도 파일에 Server Action 정의 시

예시:

```typescript
// ✅ 좋은 예 (Server Component, 지시어 없음)
// app/users/page.tsx
import { prisma } from '@/lib/prisma';

export default async function UsersPage() {
  const users = await prisma.user.findMany();
  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}

// ✅ 좋은 예 (Client Component, 필요한 경우만)
// components/Counter.tsx
'use client';
import { useState } from 'react';

export function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}

// ✅ 좋은 예 (Server Actions)
// app/todos/actions.ts
'use server';
import { prisma } from '@/lib/prisma';

export async function addTodo(title: string) {
  await prisma.todo.create({ data: { title } });
}
```

## 8. 체크리스트

코드 작성 시:
- [ ] `any` 타입 사용하지 않았는가?
- [ ] `as` 타입 캐스팅 사용하지 않았는가? (Props/DTO를 제대로 선언했는가?)
- [ ] App router 사용하고 있는가? (page router 아님)
- [ ] API route 대신 Server Component/Actions 사용 가능한가?
- [ ] Prisma를 직접 호출하는가? (불필요한 추상화 없음)
- [ ] shadcn 파일을 직접 수정하지 않았는가?
- [ ] Page에서 `use*` hook 사용하지 않았는가?
- [ ] Component 분리가 재사용 가능한가?
- [ ] 'use client'를 불필요하게 사용하지 않았는가?
- [ ] `next lint` 실행했는가?

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guny524) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

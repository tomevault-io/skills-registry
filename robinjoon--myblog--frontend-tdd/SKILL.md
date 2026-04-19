---
name: frontend-tdd
description: 프론트엔드(blog-frontend)에 TDD(Test-Driven Development) 방식으로 기능을 구현합니다. 새로운 기능 개발, 버그 수정, 리팩토링 시 사용합니다. Use when this capability is needed.
metadata:
  author: robinjoon
---

# Frontend TDD (Test-Driven Development) 스킬

$ARGUMENTS 기능을 TDD 방식으로 구현합니다.

## 작업 디렉토리

모든 작업은 `blog-frontend/` 디렉토리에서 수행합니다.

## TDD 사이클 (Red-Green-Refactor)

반드시 아래 순서를 따라야 합니다:

### 1. Red: 실패하는 테스트 작성
- 구현하려는 기능의 테스트를 **먼저** 작성합니다
- 테스트 파일 위치: 컴포넌트와 같은 폴더에 `*.test.tsx` 파일
- React Testing Library + Vitest 사용
- 테스트 실행하여 **실패 확인** (이 단계에서 실패해야 정상)

```bash
cd blog-frontend && npm test -- --run
```

### 2. Green: 최소한의 코드로 테스트 통과
- 테스트를 통과시키기 위한 **최소한의** 코드만 작성
- 완벽한 코드가 아니어도 됨
- 테스트 실행하여 **성공 확인**

### 3. Refactor: 코드 개선
- 테스트가 통과하는 상태를 유지하면서 코드 개선
- 중복 제거, 네이밍 개선, 구조 개선
- 리팩토링 후 테스트 재실행하여 **여전히 성공 확인**

## 필수 규칙

1. **테스트 없이 프로덕션 코드 작성 금지**
   - 모든 새로운 기능은 테스트가 먼저 존재해야 함

2. **한 번에 하나의 테스트만**
   - 여러 테스트를 한꺼번에 작성하지 않음
   - 하나의 테스트 → 구현 → 다음 테스트

3. **테스트 실행 필수**
   - 각 단계에서 반드시 테스트를 실행하고 결과 확인
   - Red 단계: 실패 확인
   - Green/Refactor 단계: 성공 확인

4. **작은 단위로 진행**
   - 큰 기능은 작은 테스트 케이스로 분해
   - 점진적으로 기능 완성

## 테스트 작성 가이드

### 컴포넌트 테스트 예시

```tsx
// src/components/Button/Button.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { describe, it, expect, vi } from 'vitest';
import { Button } from './Button';

describe('Button', () => {
  it('renders with text', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByRole('button', { name: /click me/i })).toBeInTheDocument();
  });

  it('calls onClick when clicked', () => {
    const handleClick = vi.fn();
    render(<Button onClick={handleClick}>Click</Button>);
    fireEvent.click(screen.getByRole('button'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
});
```

### 커스텀 훅 테스트 예시

```tsx
// src/hooks/useCounter.test.ts
import { renderHook, act } from '@testing-library/react';
import { describe, it, expect } from 'vitest';
import { useCounter } from './useCounter';

describe('useCounter', () => {
  it('should initialize with default value', () => {
    const { result } = renderHook(() => useCounter());
    expect(result.current.count).toBe(0);
  });

  it('should increment counter', () => {
    const { result } = renderHook(() => useCounter());
    act(() => {
      result.current.increment();
    });
    expect(result.current.count).toBe(1);
  });
});
```

### 비동기 컴포넌트 테스트 예시

```tsx
// src/features/posts/PostList.test.tsx
import { render, screen, waitFor } from '@testing-library/react';
import { describe, it, expect, vi } from 'vitest';
import { PostList } from './PostList';

describe('PostList', () => {
  it('displays loading state initially', () => {
    render(<PostList />);
    expect(screen.getByText(/loading/i)).toBeInTheDocument();
  });

  it('displays posts after fetch', async () => {
    render(<PostList />);
    await waitFor(() => {
      expect(screen.getByText('Post Title')).toBeInTheDocument();
    });
  });
});
```

## 테스트 쿼리 우선순위

React Testing Library의 쿼리 우선순위를 따릅니다:

1. **getByRole** - 접근성 역할 기반 (최우선)
2. **getByLabelText** - 폼 요소의 라벨
3. **getByPlaceholderText** - placeholder
4. **getByText** - 텍스트 콘텐츠
5. **getByTestId** - 최후의 수단

```tsx
// Good - 역할 기반 쿼리
screen.getByRole('button', { name: /submit/i });
screen.getByRole('textbox', { name: /email/i });

// Avoid - testId는 최후의 수단
screen.getByTestId('submit-button');
```

## 파일 구조

테스트 파일은 테스트 대상과 같은 폴더에 배치합니다:

```
src/components/Button/
├── index.ts
├── Button.tsx
├── Button.test.tsx      # 테스트 파일
└── Button.module.css
```

## 주의사항

### 1. `<style jsx>` 문법 미지원

Next.js에서 `<style jsx>` 문법은 기본 지원되지 않습니다. CSS 애니메이션은 `globals.css`에 정의하고 Tailwind arbitrary values를 사용하세요:

```css
/* globals.css */
@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}
```

```tsx
// Tailwind arbitrary values로 사용
<div className="animate-[fadeIn_0.6s_ease-out]">...</div>
```

### 2. API 서비스 테스트 시 fetch 모킹

```typescript
import { describe, it, expect, vi, beforeEach, afterEach } from "vitest";

describe("postApi", () => {
  const mockFetch = vi.fn();

  beforeEach(() => {
    vi.stubGlobal("fetch", mockFetch);
  });

  afterEach(() => {
    vi.unstubAllGlobals();
  });

  it("API 호출 테스트", async () => {
    mockFetch.mockResolvedValueOnce({
      ok: true,
      json: async () => ({ id: 1, title: "제목" }),
    });

    const result = await createPost({ title: "제목", content: "내용", authorId: 1 });

    expect(mockFetch).toHaveBeenCalledWith("/api/posts", expect.objectContaining({
      method: "POST",
    }));
  });
});
```

## 진행 보고

각 TDD 사이클마다 다음을 보고합니다:
- 현재 단계 (Red/Green/Refactor)
- 테스트 실행 결과
- 다음 단계 계획

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/robinjoon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

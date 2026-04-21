---
name: vercel-react-best-practices
description: Vercel 공식 React 성능 최적화 스킬. AI 에이전트를 위한 40+ 규칙 (waterfalls 제거, 번들 크기 최적화 등). Use when this capability is needed.
metadata:
  author: eco2-team
---

# Vercel React Best Practices Skill

> Vercel Engineering이 공식 제공하는 React & Next.js 성능 최적화 가이드 (AI 에이전트용)

## 1. 개요

Vercel의 10년 이상 React 및 Next.js 최적화 경험을 40+ 규칙으로 체계화한 공식 가이드입니다.
AI 에이전트가 코드 리팩토링, 생성, 유지보수 시 자동으로 적용할 수 있도록 설계되었습니다.

**출처**: [vercel-labs/agent-skills](https://github.com/vercel-labs/agent-skills/tree/main/skills/react-best-practices)
**버전**: 1.0.0 (2026년 1월)

## 2. 우선순위 구조

8개 카테고리, 우선순위별 분류:

```
CRITICAL (즉각적 대응 필요)
├─ 1. Waterfalls 제거 (5 규칙) ← #1 성능 킬러
└─ 2. 번들 크기 최적화 (5 규칙)

HIGH (높은 영향도)
└─ 3. 서버 사이드 성능 (7 규칙)

MEDIUM-HIGH
└─ 4. 클라이언트 데이터 페칭 (4 규칙)

MEDIUM
├─ 5. 리렌더 최적화 (7 규칙)
└─ 6. 렌더링 성능 (7 규칙)

LOW-MEDIUM
└─ 7. JavaScript 성능 (12 규칙)

LOW
└─ 8. 고급 패턴 (2 규칙)
```

## 3. 의사결정 트리

```
성능 이슈 발생
    │
    ├─ Waterfall (순차적 await)?
    │   └─ references/react-performance.md §1
    │       ├─ 1.1 await 지연
    │       ├─ 1.2 dependency-based 병렬화 (better-all)
    │       ├─ 1.3 API 체인 방지
    │       ├─ 1.4 Promise.all()
    │       └─ 1.5 Suspense boundaries
    │
    ├─ 번들 크기 과다?
    │   └─ references/react-performance.md §2
    │       ├─ 2.1 Barrel file 회피
    │       ├─ 2.2 조건부 모듈 로딩
    │       ├─ 2.3 Third-party 지연
    │       ├─ 2.4 Dynamic import
    │       └─ 2.5 Intent-based preload
    │
    ├─ 서버 성능?
    │   └─ references/react-performance.md §3
    │       ├─ React.cache() 중복 제거
    │       ├─ after() 비차단 작업
    │       └─ LRU 캐싱
    │
    └─ 클라이언트 최적화?
        └─ references/react-performance.md §4-8
```

## 4. 핵심 규칙 (Top 10)

### 4.1 Waterfalls 제거 ⚡ CRITICAL

**문제**: 순차적 await가 네트워크 레이턴시 누적

```typescript
// ❌ 300ms + 300ms = 600ms
const user = await fetchUser();
const posts = await fetchPosts(user.id);

// ✅ max(300ms, 300ms) = 300ms (better-all 사용)
import { all } from 'better-all';

const { user, posts } = await all({
  async user() { return fetchUser(); },
  async posts() {
    return fetchPosts((await this.$.user).id);
  },
});
```

**영향**: 2-10배 개선

### 4.2 Barrel File Import 회피 📦 CRITICAL

**문제**: `import { Icon } from '@/icons'`가 전체 icons 폴더를 번들에 포함

```typescript
// ❌ 전체 icons/ 폴더가 번들에 포함
import { CheckIcon } from '@/icons';

// ✅ 필요한 파일만 포함
import { CheckIcon } from '@/icons/CheckIcon';
```

**특히 주의**: lucide-react, @mui/material, react-icons

### 4.3 React.cache() 중복 제거 ⚡ HIGH

**문제**: 같은 데이터를 여러 컴포넌트에서 요청

```typescript
// ✅ 요청당 1회만 실행
import { cache } from 'react';

export const getUser = cache(async (id: string) => {
  return await db.user.findUnique({ where: { id } });
});
```

**영향**: 중복 DB 쿼리 제거

### 4.4 Dynamic Import (Heavy Components) 📦 CRITICAL

```typescript
// ❌ 초기 번들에 포함
import HeavyChart from './HeavyChart';

// ✅ 사용 시점에 로드
const HeavyChart = dynamic(() => import('./HeavyChart'), {
  loading: () => <Skeleton />,
});
```

### 4.5 Passive Event Listeners 🎯 MEDIUM-HIGH

```typescript
// ❌ 스크롤 성능 저하
element.addEventListener('scroll', handler);

// ✅ 브라우저 최적화 허용
element.addEventListener('scroll', handler, { passive: true });
```

## 5. 이코에코 프로젝트 적용

### 5.1 Agent 페이지 최적화

**현재 이슈**:
```typescript
// useAgentChat.ts - Waterfall 존재
const newChat = await createNewChat();
const imageUrl = await uploadImage();
const response = await sendMessage(chatId, { message, image_url: imageUrl });
```

**개선 (better-all)**:
```typescript
import { all } from 'better-all';

const { newChat, imageUrl, response } = await all({
  async newChat() {
    return await createNewChat();
  },
  async imageUrl() {
    return selectedImage ? await uploadImage() : null;
  },
  async response() {
    const chat = await this.$.newChat;
    const img = await this.$.imageUrl;
    return await sendMessage(chat.id, { message, image_url: img });
  },
});
```

### 5.2 번들 크기 최적화

**현재 이슈**: lucide-react 전체 import

```typescript
// ❌ 잘못된 패턴
import { Send, Image, X } from 'lucide-react';

// ✅ 올바른 패턴
import Send from 'lucide-react/dist/esm/icons/send';
import Image from 'lucide-react/dist/esm/icons/image';
import X from 'lucide-react/dist/esm/icons/x';
```

**자동화**: vite.config.ts에 treeshaking 설정 추가

### 5.3 IndexedDB 캐싱 (LRU)

**Vercel Fluid Compute와 유사한 패턴**:

```typescript
// messageDB.ts - 이미 LRU 구현됨 ✅
async cleanup(userId, sessionId, {
  committedRetentionMs: 30000,  // 30초 LRU
  ttlMs: 7 * 24 * 60 * 60 * 1000,  // 7일 TTL
});
```

## 6. 참조 문서

| 파일 | 내용 |
|------|------|
| `references/react-performance.md` | Vercel 공식 40+ 규칙 전문 |

## 7. 자동 검증

### 7.1 ESLint 플러그인 (권장)

```bash
# Vercel React Best Practices ESLint Plugin (향후 제공 예정)
npm install -D eslint-plugin-vercel-react
```

### 7.2 Bundle Analyzer

```bash
# vite-plugin-bundle-analyzer
npm install -D rollup-plugin-visualizer

# package.json
"analyze": "vite build && rollup-plugin-visualizer"
```

## 8. 체크리스트

코드 리뷰 시:

```
[ ] 순차적 await 제거 (Promise.all 또는 better-all)
[ ] Barrel file import 회피
[ ] Dynamic import (100KB+ 컴포넌트)
[ ] React.cache() 중복 제거 (서버 컴포넌트)
[ ] Passive event listeners (스크롤, 터치)
[ ] Bundle size < 500KB (main chunk)
```

## 9. 추가 리소스

- **Vercel Blog**: [Introducing React Best Practices](https://vercel.com/blog/introducing-react-best-practices)
- **GitHub Repo**: [vercel-labs/agent-skills](https://github.com/vercel-labs/agent-skills)
- **Better-all Library**: [shuding/better-all](https://github.com/shuding/better-all)

## 10. 주의사항

### 10.1 Premature Optimization

**원칙**: 측정 후 최적화

```typescript
// 먼저 프로파일링
// React DevTools Profiler → 느린 렌더링 찾기
// Network 탭 → Waterfall 확인
// Bundle Analyzer → 큰 dependencies 찾기

// 그 다음 최적화
```

### 10.2 Vercel-specific Features

일부 규칙은 Vercel 환경에 최적화됨:
- Fluid Compute (LRU 캐싱)
- Edge Functions
- `after()` API (Next.js 15+)

다른 호스팅 환경에서는 적용 불가할 수 있음.

## 11. 업데이트 추적

Vercel agent-skills 저장소 모니터링:

```bash
# GitHub Watch 설정
# https://github.com/vercel-labs/agent-skills
# Watch → Custom → Releases only

# 새 버전 릴리즈 시 references/react-performance.md 업데이트
```

## 12. 이코에코 프로젝트 적용 로드맵

### Phase 1 (즉시 적용)
- [x] Bundle size 분석 (rollup-plugin-visualizer)
- [ ] lucide-react imports 수정
- [ ] better-all 도입 (useAgentChat waterfall 제거)

### Phase 2 (다음 스프린트)
- [ ] Dynamic imports (Heavy components)
- [ ] React.cache() (서버 컴포넌트 전환 시)
- [ ] Passive listeners (스크롤 최적화)

### Phase 3 (장기)
- [ ] ESLint plugin 도입 (출시 후)
- [ ] Performance budget CI/CD
- [ ] Lighthouse CI 통합

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eco2-team) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

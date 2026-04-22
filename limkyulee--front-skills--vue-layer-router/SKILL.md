---
name: vue-layer-router
description: Vue3 프로젝트의 src/router 레이어를 생성하는 스킬. Vue Router 4 기반 라우팅 정의, 인증 가드, route meta 타입을 실제 파일로 생성한다. 사용자가 'router 레이어 만들어줘', '라우팅 설정 추가해줘', '인증 가드 만들어줘', 'route guard 설정', 'Vue Router 초기화', '라우터 구조 잡아줘' 같은 말을 할 때 이 스킬을 사용할 것. vue-framework-gen 오케스트레이터에서 세 번째로 실행된다. 기존 Vue3 프로젝트에 router 레이어만 단독으로 추가할 때도 사용 가능하다. Use when this capability is needed.
metadata:
  author: limkyulee
---

# Vue Layer — Router (라우팅 + 인증 가드)

src/router 디렉터리와 Vue Router 4 기반 라우팅 시스템을 생성한다.

---

## 입력 컨텍스트 확인

```
PROJECT_PATH : 프로젝트 루트 경로
PRESET       : admin-dashboard | public-portal | data-platform | chat-application
CONFIG       : { vueRouter: true | false, typescript: true | false }
```

**CONFIG.vueRouter = false 이면 이 레이어 전체를 건너뛴다.**

---

## 패키지 설치 체크

```bash
cd [PROJECT_PATH]
# vue-router 설치 여부 확인
if ! node -e "require('vue-router')" 2>/dev/null; then
  [PKG_MANAGER] add vue-router@^4.4.0
fi
```

PRESET에 따라 기본 라우트 구성이 달라진다.

---

## 생성 파일 목록

```
src/router/
├── index.ts     — 라우터 인스턴스 + 라우트 정의
├── guards.ts    — 인증 가드 (beforeEach)
└── types.ts     — RouteMeta 타입 확장
```

---

## 파일 내용 기준

### `src/router/types.ts`
```ts
import 'vue-router'

// route meta 타입 확장 — 모든 라우트에서 타입 안전하게 사용
declare module 'vue-router' {
  interface RouteMeta {
    requiresAuth: boolean        // 인증 필요 여부
    layout?: 'default' | 'auth' | 'admin' | 'data' | 'chat'
    title?: string               // 브라우저 탭 타이틀
    roles?: string[]             // 허용 역할 (선택)
  }
}
```

### `src/router/guards.ts`
```ts
import type { Router } from 'vue-router'
import { useAuthStore } from '@/stores/auth.store'

export function setupGuards(router: Router): void {
  router.beforeEach(async (to, _from, next) => {
    const authStore = useAuthStore()

    // 브라우저 탭 타이틀 업데이트
    if (to.meta.title) {
      document.title = `${to.meta.title} | ${import.meta.env.VITE_APP_TITLE}`
    }

    // 인증 불필요 라우트는 바로 통과
    if (!to.meta.requiresAuth) {
      // 이미 로그인 상태로 로그인 페이지 접근 시 홈으로
      if (to.path === '/login' && authStore.isAuthenticated) {
        return next('/')
      }
      return next()
    }

    // 인증 필요 + 미인증 → 로그인으로
    if (!authStore.isAuthenticated) {
      return next({ path: '/login', query: { redirect: to.fullPath } })
    }

    // 역할 검사 (meta.roles 있을 경우)
    if (to.meta.roles && !authStore.hasAnyRole(to.meta.roles)) {
      return next('/403')
    }

    next()
  })
}
```

### `src/router/index.ts` — PRESET에 따라 기본 라우트 분기

**공통 라우트 (모든 preset 포함):**
```ts
import { createRouter, createWebHashHistory } from 'vue-router'
import { setupGuards } from './guards'
import './types'  // RouteMeta 타입 로드

const router = createRouter({
  history: createWebHashHistory(),
  routes: [
    {
      path: '/login',
      component: () => import('@/pages/LoginPage.vue'),
      meta: { requiresAuth: false, layout: 'auth', title: '로그인' }
    },
    {
      path: '/:pathMatch(.*)*',
      component: () => import('@/pages/NotFoundPage.vue'),
      meta: { requiresAuth: false }
    },
    // [PRESET_ROUTES] ← 아래 preset별 분기로 삽입
  ]
})

setupGuards(router)
export default router
```

**preset별 추가 라우트:**

| Preset | 추가 라우트 |
|--------|-----------|
| `admin-dashboard` | `/ → DashboardPage`, `/users → UsersPage` (requiresAuth: true, layout: 'admin') |
| `public-portal` | `/ → HomePage`, `/about → AboutPage` (requiresAuth: false, layout: 'default') |
| `data-platform` | `/overview → OverviewPage`, `/detail/:id → DetailPage` (requiresAuth: true, layout: 'data') |
| `chat-application` | `/ → ChatPage` (requiresAuth: true, layout: 'chat') |

실제 파일 생성 시 PRESET 값에 맞는 라우트를 index.ts에 삽입한다.

---

## 의존 관계

- `stores/auth.store.ts` — `useAuthStore()`, `isAuthenticated`, `hasAnyRole()` 필요
  → vue-layer-store가 먼저 실행된 경우 import가 정상 동작
  → 단독 실행 시 store stub을 임시 생성하거나 주석 처리

---

## bash 실행 원칙

1. `mkdir -p [PROJECT_PATH]/src/router`
2. types.ts → guards.ts → index.ts 순으로 생성

---

## 완료 확인

```
✅ [router] 레이어 생성 완료
   src/router/ 3개 파일 (index, guards, types)
   적용 preset: [PRESET]
```

다음 실행: `vue-layer-store`

---
> Source: [limkyulee/front-skills](https://github.com/limkyulee/front-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->

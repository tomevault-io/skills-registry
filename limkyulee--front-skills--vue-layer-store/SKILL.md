---
name: vue-layer-store
description: Vue3 프로젝트의 src/stores 레이어를 생성하는 스킬. Pinia 기반 전역 상태 관리 (auth store, user store)를 실제 파일로 생성한다. 사용자가 'store 레이어 만들어줘', 'Pinia store 생성해줘', 'auth store 추가해줘', '전역 상태 관리 설정', '로그인 상태 store', 'user store 만들어줘' 같은 말을 할 때 이 스킬을 사용할 것. vue-framework-gen 오케스트레이터에서 네 번째로 실행된다. 기존 Vue3 프로젝트에 store 레이어만 단독으로 추가할 때도 사용 가능하다. Use when this capability is needed.
metadata:
  author: limkyulee
---

# Vue Layer — Store (전역 상태 관리)

src/stores 디렉터리와 Pinia 기반 전역 상태 스토어를 생성한다.

---

## 입력 컨텍스트 확인

```
PROJECT_PATH : 프로젝트 루트 경로
CONFIG       : { pinia: true | false }
```

**CONFIG.pinia = false 이면 이 레이어 전체를 건너뛴다.**

---

## 패키지 설치 체크

```bash
cd [PROJECT_PATH]
# pinia 설치 여부 확인
if ! node -e "require('pinia')" 2>/dev/null; then
  [PKG_MANAGER] add pinia@^2.2.0
fi
```

---

## 생성 파일 목록

```
src/stores/
├── auth.store.ts   — 인증 상태 (토큰, 로그인/로그아웃, 역할)
└── user.store.ts   — 현재 사용자 프로필 상태
```

---

## 파일 내용 기준

### `src/stores/auth.store.ts`
```ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'
import apiClient from '@/api/client'
import type { LoginRequest, LoginResponse } from '@/types/api'

export const useAuthStore = defineStore('auth', () => {
  // 상태
  const accessToken  = ref<string | null>(localStorage.getItem('access_token'))
  const refreshToken = ref<string | null>(localStorage.getItem('refresh_token'))
  const userRoles    = ref<string[]>([])

  // 파생 상태
  const isAuthenticated = computed(() => !!accessToken.value)

  // 역할 검사
  function hasAnyRole(roles: string[]): boolean {
    return roles.some(role => userRoles.value.includes(role))
  }

  // 로그인
  async function login(payload: LoginRequest): Promise<void> {
    const { data } = await apiClient.post<LoginResponse>('/auth/login', payload)
    accessToken.value  = data.accessToken
    refreshToken.value = data.refreshToken
    userRoles.value    = data.roles ?? []
    localStorage.setItem('access_token',  data.accessToken)
    localStorage.setItem('refresh_token', data.refreshToken)
  }

  // 로그아웃
  function logout(): void {
    accessToken.value  = null
    refreshToken.value = null
    userRoles.value    = []
    localStorage.removeItem('access_token')
    localStorage.removeItem('refresh_token')
  }

  return { accessToken, isAuthenticated, userRoles, hasAnyRole, login, logout }
}, {
  // pinia-plugin-persistedstate 사용 시 활성화
  // persist: { paths: ['accessToken', 'userRoles'] }
})
```

### `src/stores/user.store.ts`
```ts
import { defineStore } from 'pinia'
import { ref } from 'vue'
import apiClient from '@/api/client'
import type { UserProfile } from '@/types/common'

export const useUserStore = defineStore('user', () => {
  // 상태
  const profile  = ref<UserProfile | null>(null)
  const isLoading = ref(false)

  // 현재 로그인 사용자 프로필 조회
  async function fetchProfile(): Promise<void> {
    isLoading.value = true
    try {
      const { data } = await apiClient.get<UserProfile>('/users/me')
      profile.value = data
    } finally {
      isLoading.value = false
    }
  }

  // 상태 초기화 (로그아웃 시 호출)
  function $reset(): void {
    profile.value  = null
    isLoading.value = false
  }

  return { profile, isLoading, fetchProfile, $reset }
})
```

---

## Store 설계 원칙

- Store는 **orchestration 역할만** 수행한다 (API 호출 조율 + 상태 보관)
- 비즈니스 로직은 features 레이어의 composable에 위치한다
- Store 간 직접 참조 금지 — 필요 시 action 호출로 간접 연동
- `$reset()` 패턴으로 상태 초기화를 명시적으로 제공

---

## 의존 관계

- `src/api/client.ts` — apiClient import (vue-layer-api 선행 필요)
- `src/types/common.ts`, `src/types/api.ts` — 타입 참조 (vue-layer-shared 선행 필요)
  → 단독 실행 시 타입을 인라인으로 임시 정의하거나 주석 처리

---

## bash 실행 원칙

1. `mkdir -p [PROJECT_PATH]/src/stores`
2. auth.store.ts → user.store.ts 순 생성

---

## 완료 확인

```
✅ [store] 레이어 생성 완료
   src/stores/ 2개 파일 (auth.store, user.store)
```

다음 실행: `vue-layer-shared`

---
> Source: [limkyulee/front-skills](https://github.com/limkyulee/front-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->

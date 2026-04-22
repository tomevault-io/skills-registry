---
name: vue-layer-api
description: Vue3 프로젝트의 src/api 레이어를 생성하는 스킬. Axios 기반 HTTP 클라이언트, 요청/응답 인터셉터, HTTP 상태별 에러 핸들러를 실제 파일로 생성한다. 사용자가 'api 레이어 만들어줘', 'axios 클라이언트 설정해줘', '인터셉터 추가해줘', 'HTTP 클라이언트 생성', 'api 모듈 만들어줘', '에러 핸들러 추가' 같은 말을 할 때 이 스킬을 사용할 것. vue-framework-gen 오케스트레이터에서 두 번째로 실행된다. 기존 Vue3 프로젝트에 api 레이어만 단독으로 추가할 때도 사용 가능하다. Use when this capability is needed.
metadata:
  author: limkyulee
---

# Vue Layer — API (HTTP 클라이언트)

src/api 디렉터리와 Axios 기반 HTTP 통신 모듈 전체를 생성한다.

---

## 입력 컨텍스트 확인

```
PROJECT_PATH : 프로젝트 루트 경로
```

---

## 패키지 설치 체크

```bash
cd [PROJECT_PATH]
# axios 설치 여부 확인
if ! node -e "require('axios')" 2>/dev/null; then
  [PKG_MANAGER] add axios@^1.7.0
fi
```

단독 실행 시 사용자에게 수집. 오케스트레이터에서는 자동 전달.

---

## 생성 파일 목록

```
src/api/
├── client.ts        — Axios 인스턴스 + 기본 설정
├── interceptor.ts   — 요청/응답 인터셉터 (토큰, 에러)
└── errorHandler.ts  — HTTP 상태별 에러 처리 + 메시지 매핑
```

---

## 파일 내용 기준

### `src/api/client.ts`
```ts
import axios from 'axios'
import { setupInterceptors } from './interceptor'

// Axios 인스턴스 생성
const apiClient = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL ?? '/api',
  timeout: 10_000,
  headers: {
    'Content-Type': 'application/json',
  },
})

// 인터셉터 등록
setupInterceptors(apiClient)

export default apiClient
```

### `src/api/interceptor.ts`
```ts
import type { AxiosInstance, InternalAxiosRequestConfig, AxiosResponse } from 'axios'
import { handleApiError } from './errorHandler'

export function setupInterceptors(client: AxiosInstance): void {
  // 요청 인터셉터 — Authorization 토큰 주입
  client.interceptors.request.use(
    (config: InternalAxiosRequestConfig) => {
      const token = localStorage.getItem('access_token')
      if (token) {
        config.headers.Authorization = `Bearer ${token}`
      }
      return config
    },
    (error) => Promise.reject(error)
  )

  // 응답 인터셉터 — 에러 처리 + 401 리다이렉트
  client.interceptors.response.use(
    (response: AxiosResponse) => response,
    async (error) => {
      // 401: 토큰 만료 → 로그인 페이지로
      if (error.response?.status === 401) {
        localStorage.removeItem('access_token')
        window.location.href = '/login'
        return Promise.reject(error)
      }
      return Promise.reject(handleApiError(error))
    }
  )
}
```

### `src/api/errorHandler.ts`
```ts
import type { AxiosError } from 'axios'

// HTTP 상태 코드별 기본 에러 메시지
const HTTP_ERROR_MESSAGES: Record<number, string> = {
  400: '잘못된 요청입니다.',
  401: '인증이 필요합니다.',
  403: '접근 권한이 없습니다.',
  404: '요청한 리소스를 찾을 수 없습니다.',
  408: '요청 시간이 초과되었습니다.',
  409: '요청이 충돌했습니다.',
  422: '입력 데이터를 처리할 수 없습니다.',
  429: '요청이 너무 많습니다. 잠시 후 다시 시도해주세요.',
  500: '서버 오류가 발생했습니다.',
  502: '게이트웨이 오류가 발생했습니다.',
  503: '서비스를 일시적으로 사용할 수 없습니다.',
}

export interface ApiError {
  status:  number | null
  message: string
  data:    unknown
  original: unknown
}

export function handleApiError(error: AxiosError): ApiError {
  const status  = error.response?.status ?? null
  const data    = error.response?.data

  // 서버가 내려준 메시지 우선, 없으면 상태 코드 기본 메시지
  const serverMessage =
    (data as Record<string, string>)?.message ??
    (data as Record<string, string>)?.error ??
    null

  const message =
    serverMessage ??
    (status ? HTTP_ERROR_MESSAGES[status] : null) ??
    '알 수 없는 오류가 발생했습니다.'

  return { status, message, data, original: error }
}
```

---

## 의존 관계

- `src/types/api.ts` — `ApiError` 타입을 공유할 경우 vue-layer-shared 이후 참조
- `stores/auth.store.ts` — 토큰 갱신 로직 필요 시 vue-layer-store와 연동

---

## bash 실행 원칙

1. `mkdir -p [PROJECT_PATH]/src/api`
2. 파일별 `cat > ... << 'EOF'` 로 작성

---

## 완료 확인

```
✅ [api] 레이어 생성 완료
   src/api/ 3개 파일 (client, interceptor, errorHandler)
```

다음 실행: `vue-layer-router`

---
> Source: [limkyulee/front-skills](https://github.com/limkyulee/front-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->

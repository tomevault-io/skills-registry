---
name: vue-layer-app
description: Vue3 프로젝트의 루트 설정 파일과 src/app 부트스트랩 레이어를 생성하는 스킬. package.json, vite.config.ts, tsconfig.json, .env.example, eslint, prettier, main.ts, App.vue를 실제 파일로 생성한다. 사용자가 'app 레이어 만들어줘', '프로젝트 루트 설정 생성', 'main.ts 만들어줘', 'vue 프로젝트 초기화', '부트스트랩 설정' 같은 말을 할 때 이 스킬을 사용할 것. vue-framework-gen 오케스트레이터에서 첫 번째로 실행된다. Use when this capability is needed.
metadata:
  author: limkyulee
---

# Vue Layer — App (부트스트랩 + 루트 설정)

프로젝트 루트 설정 파일 전체와 src/app 부트스트랩을 생성한다.

---

## ⚠️ 경로 설정 원칙

PROJECT_PATH는 오케스트레이터에서 전달받는다.
단독 실행 시에는 반드시 bash로 현재 디렉토리를 감지한다:

```bash
CURRENT_DIR=$(pwd)
echo "현재 경로: $CURRENT_DIR"
# PROJECT_PATH = $CURRENT_DIR/vue/[PROJECT_NAME]
```

절대로 하드코딩된 홈 경로나 루트 경로에 직접 생성하지 않는다.

---

## 입력 컨텍스트 확인

오케스트레이터에서 전달받거나, 단독 실행 시 사용자에게 직접 수집한다:

```
PROJECT_NAME  : 프로젝트명 (예: my-app)
PROJECT_PATH  : [CURRENT_DIR]/vue/[PROJECT_NAME]  ← bash pwd 결과 기반
PKG_MANAGER   : npm | pnpm(기본) | yarn | bun
CONFIG        : {
  typescript, jsx, vueRouter, pinia,
  vitest, e2e, linter, prettier, devtools
}
```

단독 실행 시 CONFIG가 없으면 ask_user_input 위젯으로 각 항목을 질문한다.

---

## 패키지 설치 체크

파일 생성 전 아래를 확인한다:

```bash
ls [PROJECT_PATH]/node_modules 2>/dev/null || echo "node_modules 없음 — 생성 후 install 실행"
```

---

## CONFIG 기반 생성 파일 분기

| 파일 | 생성 조건 |
|------|---------|
| `tsconfig.json`, `tsconfig.node.json` | CONFIG.typescript = true |
| `.eslintrc.cjs` (또는 `eslint.config.js`) | CONFIG.linter = eslint 또는 eslint+oxlint |
| `oxlintrc.json` | CONFIG.linter = oxlint 또는 eslint+oxlint |
| `.prettierrc` | CONFIG.prettier = true |
| `cypress.config.ts` | CONFIG.e2e = cypress |
| `playwright.config.ts` | CONFIG.e2e = playwright |
| `nightwatch.conf.js` | CONFIG.e2e = nightwatch |

---

## 생성 파일 목록

### 루트 파일

| 파일 | 설명 |
|------|------|
| `package.json` | 의존성 + 스크립트 |
| `vite.config.ts` | Vite 빌드 설정, `@/` 경로 별칭 |
| `tsconfig.json` | TypeScript strict 모드, path alias |
| `tsconfig.node.json` | Vite 노드 환경 타입 설정 |
| `.env.example` | 환경변수 템플릿 |
| `.eslintrc.cjs` | ESLint + Vue3 + TypeScript 규칙 |
| `.prettierrc` | 코드 포맷 규칙 |
| `.gitignore` | node_modules, dist, .env 등 |
| `index.html` | Vite 진입점 HTML |

### src/app

| 파일 | 설명 |
|------|------|
| `src/main.ts` | Vue 앱 생성, 플러그인 등록 |
| `src/App.vue` | 루트 컴포넌트, RouterView |

---

## 파일 내용 기준

### `vite.config.ts`
```ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import { fileURLToPath, URL } from 'node:url'

export default defineConfig({
  plugins: [vue()],
  resolve: {
    alias: {
      '@': fileURLToPath(new URL('./src', import.meta.url))
    }
  },
  server: {
    port: 3000,
    proxy: {
      '/api': {
        target: process.env.VITE_API_BASE_URL,
        changeOrigin: true
      }
    }
  }
})
```

### `tsconfig.json`
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": true,
    "jsx": "preserve",
    "lib": ["ES2022", "DOM", "DOM.Iterable"],
    "baseUrl": ".",
    "paths": { "@/*": ["./src/*"] },
    "resolveJsonModule": true,
    "isolatedModules": true,
    "verbatimModuleSyntax": true,
    "skipLibCheck": true
  },
  "include": ["src/**/*", "vite.config.ts"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

### `.env.example`
```
VITE_API_BASE_URL=http://localhost:8080
VITE_APP_TITLE=[PROJECT_NAME]
VITE_APP_ENV=development
```

### `src/main.ts`
```ts
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import router from '@/router'
import App from './App.vue'

import '@/shared/styles/reset.css'
import '@/shared/styles/tokens.css'
import '@/shared/styles/global.css'

const app = createApp(App)
app.use(createPinia())
app.use(router)
app.mount('#app')
```

### `src/App.vue`
```vue
<script setup lang="ts">
// 레이아웃은 route.meta.layout으로 결정
</script>
<template>
  <RouterView />
</template>
```

### `index.html`
```html
<!DOCTYPE html>
<html lang="ko">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>%VITE_APP_TITLE%</title>
  </head>
  <body>
    <div id="app"></div>
    <script type="module" src="/src/main.ts"></script>
  </body>
</html>
```

---

## bash 실행 원칙

1. `mkdir -p [PROJECT_PATH]/src` 로 디렉터리 먼저 생성
2. 파일별로 개별 bash 호출로 생성
3. CONFIG 값에 따라 파일 생성 분기

### 패키지 설치

```bash
cd [PROJECT_PATH]
if [ ! -d "node_modules" ]; then
  [PKG_MANAGER] install
fi
```

---

## 완료 확인

```
✅ [app] 레이어 생성 완료
   루트 설정 9개 + src/ 2개 = 총 11개 파일
   생성 위치: [PROJECT_PATH]
```

다음 실행: `vue-layer-api`

---
> Source: [limkyulee/front-skills](https://github.com/limkyulee/front-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->

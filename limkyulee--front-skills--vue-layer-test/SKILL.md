---
name: vue-layer-test
description: Vue3 프로젝트의 테스트 환경을 설정하는 스킬. CONFIG에 따라 Vitest(단위 테스트)와 E2E 테스트(Cypress / Nightwatch / Playwright) 설정 파일, 샘플 테스트 파일을 실제로 생성하고 필요한 패키지를 설치한다. 사용자가 '테스트 설정 추가해줘', 'vitest 설정해줘', 'cypress 붙여줘', 'playwright 설정', 'e2e 테스트 추가', '테스트 레이어 만들어줘', '단위 테스트 환경 잡아줘' 같은 말을 할 때 이 스킬을 사용할 것. vue-framework-gen 오케스트레이터에서 여섯 번째로 실행된다. CONFIG.vitest와 CONFIG.e2e가 모두 none이면 이 레이어 전체를 건너뛴다. Use when this capability is needed.
metadata:
  author: limkyulee
---

# Vue Layer — Test (테스트 환경 설정)

Vitest 단위 테스트와 E2E 테스트 환경을 설정하고 샘플 테스트 파일을 생성한다.

---

## 입력 컨텍스트 확인

```
PROJECT_PATH : 프로젝트 루트 경로
CONFIG       : {
  typescript: true | false
  vitest:     true | false
  e2e:        none | cypress | nightwatch | playwright
}
```

**CONFIG.vitest = false 이고 CONFIG.e2e = none 이면 이 레이어 전체를 건너뛴다.**

---

## 패키지 설치 체크

```bash
cd [PROJECT_PATH]

# Vitest
if CONFIG.vitest = true; then
  if ! node -e "require('vitest')" 2>/dev/null; then
    [PKG_MANAGER] add -D vitest @vue/test-utils jsdom @vitest/coverage-v8
  fi
fi

# E2E
if CONFIG.e2e = cypress; then
  if ! node -e "require('cypress')" 2>/dev/null; then
    [PKG_MANAGER] add -D cypress @cypress/vue
  fi
fi

if CONFIG.e2e = playwright; then
  if ! node -e "require('@playwright/test')" 2>/dev/null; then
    [PKG_MANAGER] add -D @playwright/test
    npx playwright install
  fi
fi

if CONFIG.e2e = nightwatch; then
  if ! node -e "require('nightwatch')" 2>/dev/null; then
    [PKG_MANAGER] add -D nightwatch @nightwatch/vue
  fi
fi
```

---

## 생성 파일 목록

### Vitest 설정 (CONFIG.vitest = true)

```
vitest.config.ts          — Vitest 설정 파일
src/__tests__/
└── example.spec.ts       — 샘플 단위 테스트
src/shared/ui/button/
└── BaseButton.spec.ts    — BaseButton 컴포넌트 샘플 테스트
```

### E2E 설정 (CONFIG.e2e 값에 따라)

**cypress:**
```
cypress.config.ts
cypress/
├── e2e/
│   └── example.cy.ts     — 샘플 E2E 시나리오
├── support/
│   ├── commands.ts
│   └── e2e.ts
└── fixtures/
    └── example.json
```

**playwright:**
```
playwright.config.ts
e2e/
└── example.spec.ts       — 샘플 E2E 시나리오
```

**nightwatch:**
```
nightwatch.conf.js
tests/e2e/
└── example.js            — 샘플 E2E 시나리오
```

---

## 파일 내용 기준

### `vitest.config.ts`
```ts
import { defineConfig } from 'vitest/config'
import vue from '@vitejs/plugin-vue'
import { fileURLToPath, URL } from 'node:url'

export default defineConfig({
  plugins: [vue()],
  test: {
    environment: 'jsdom',      // 브라우저 환경 시뮬레이션
    globals: true,             // describe, it, expect 전역 사용
    coverage: {
      provider: 'v8',
      reporter: ['text', 'lcov'],
      include: ['src/**/*.{ts,vue}'],
      exclude: ['src/**/*.spec.ts', 'src/**/*.d.ts']
    }
  },
  resolve: {
    alias: { '@': fileURLToPath(new URL('./src', import.meta.url)) }
  }
})
```

### `src/__tests__/example.spec.ts` — 단위 테스트 샘플
```ts
import { describe, it, expect } from 'vitest'

/**
 * 샘플 단위 테스트
 * - 실제 테스트는 src/features/[domain]/ 또는 src/shared/ 하위에 작성
 * - 파일명 규칙: [대상파일명].spec.ts
 */
describe('example', () => {
  it('should pass', () => {
    expect(1 + 1).toBe(2)
  })
})
```

### `src/shared/ui/button/BaseButton.spec.ts` — 컴포넌트 테스트 샘플
```ts
import { describe, it, expect } from 'vitest'
import { mount } from '@vue/test-utils'
import BaseButton from './BaseButton.vue'

describe('BaseButton', () => {
  it('renders slot content', () => {
    const wrapper = mount(BaseButton, {
      slots: { default: '저장' }
    })
    expect(wrapper.text()).toBe('저장')
  })

  it('emits click when not loading', async () => {
    const wrapper = mount(BaseButton, { props: { loading: false } })
    await wrapper.trigger('click')
    expect(wrapper.emitted('click')).toBeTruthy()
  })

  it('does not emit click when loading', async () => {
    const wrapper = mount(BaseButton, { props: { loading: true } })
    await wrapper.trigger('click')
    expect(wrapper.emitted('click')).toBeFalsy()
  })
})
```

### `cypress.config.ts`
```ts
import { defineConfig } from 'cypress'

export default defineConfig({
  e2e: {
    baseUrl: 'http://localhost:3000',
    supportFile: 'cypress/support/e2e.ts',
    specPattern: 'cypress/e2e/**/*.cy.ts',
    video: false,
    screenshotOnRunFailure: true
  }
})
```

### `cypress/e2e/example.cy.ts` — Cypress E2E 샘플
```ts
/**
 * 샘플 E2E 테스트
 * - 실제 시나리오는 사용자 흐름 기반으로 작성
 * - 예: 로그인 → 목록 조회 → 상세 확인
 */
describe('Example E2E', () => {
  it('visits the app', () => {
    cy.visit('/')
    cy.url().should('include', '/')
  })
})
```

### `playwright.config.ts`
```ts
import { defineConfig, devices } from '@playwright/test'

export default defineConfig({
  testDir: './e2e',
  fullyParallel: true,
  retries: process.env.CI ? 2 : 0,
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry'
  },
  projects: [
    { name: 'chromium', use: { ...devices['Desktop Chrome'] } }
  ],
  webServer: {
    command: '[PKG_MANAGER] dev',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI
  }
})
```

### `e2e/example.spec.ts` — Playwright E2E 샘플
```ts
import { test, expect } from '@playwright/test'

test('visits the app', async ({ page }) => {
  await page.goto('/')
  await expect(page).toHaveURL('/')
})
```

---

## package.json scripts 추가

기존 scripts에 아래를 병합한다 (덮어쓰지 않고 추가):

```json
// CONFIG.vitest = true 시
"test:unit": "vitest",
"test:unit:run": "vitest run",
"coverage": "vitest run --coverage",

// CONFIG.e2e = cypress 시
"test:e2e": "cypress run",
"test:e2e:open": "cypress open",

// CONFIG.e2e = playwright 시
"test:e2e": "playwright test",
"test:e2e:ui": "playwright test --ui",

// CONFIG.e2e = nightwatch 시
"test:e2e": "nightwatch",
```

---

## 테스트 파일 위치 규칙

생성된 샘플과 주석으로 팀 컨벤션을 명시한다:

```
단위 테스트:
  src/shared/ui/button/BaseButton.spec.ts    ← 컴포넌트 옆에 위치
  src/features/auth/useAuth.spec.ts          ← composable 옆에 위치
  src/shared/utils/format.spec.ts            ← util 옆에 위치

E2E 테스트:
  cypress/e2e/[시나리오명].cy.ts             ← 사용자 흐름 기반
  e2e/[시나리오명].spec.ts                   ← playwright
```

---

## 완료 확인

```
✅ [test] 레이어 생성 완료

📁 생성된 파일:
  [vitest 선택 시]
    vitest.config.ts
    src/__tests__/example.spec.ts
    src/shared/ui/button/BaseButton.spec.ts

  [e2e 선택 시 — cypress 예시]
    cypress.config.ts
    cypress/e2e/example.cy.ts
    cypress/support/commands.ts
    cypress/support/e2e.ts
    cypress/fixtures/example.json

📦 설치된 패키지:
  vitest @vue/test-utils jsdom @vitest/coverage-v8  (vitest 선택 시)
  cypress @cypress/vue                               (cypress 선택 시)

📋 다음 단계:
  - pnpm test:unit         → 단위 테스트 실행
  - pnpm coverage          → 커버리지 리포트
  - pnpm test:e2e:open     → E2E 테스트 UI 실행
```

---
> Source: [limkyulee/front-skills](https://github.com/limkyulee/front-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->

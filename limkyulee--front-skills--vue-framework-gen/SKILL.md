---
name: vue-framework-gen
description: Vue3 + TypeScript 프론트엔드 공통 프레임워크 전체를 자동 스캐폴딩하는 오케스트레이터 스킬. 프로젝트 정보를 수집한 뒤 레이어 스킬들(vue-layer-app, vue-layer-api, vue-layer-router, vue-layer-store, vue-layer-shared, vue-layer-domain)을 순서대로 실행해 완전한 프로젝트를 생성한다. 사용자가 '프레임워크 만들어줘', 'Vue 프로젝트 초기 구조 잡아줘', '프론트엔드 보일러플레이트 생성해줘', 'FSD 구조로 프로젝트 만들어줘', 'Vue 아키텍처 기반으로 새 프로젝트 만들어줘', '프레임워크 스캐폴딩' 같은 말을 할 때 반드시 이 스킬을 사용할 것. 개별 레이어만 추가하고 싶으면 각 vue-layer-* 스킬을 단독으로 실행할 수 있다. Use when this capability is needed.
metadata:
  author: limkyulee
---

# Vue Framework Generator — 오케스트레이터

각 레이어 스킬을 순서대로 실행해 Vue3 + TypeScript 프로젝트 전체를 생성한다.

---

## ⚠️ 핵심 원칙 — 반드시 준수

1. **모든 인터뷰 단계는 사용자가 직접 선택해야 한다.** 자동 선택 절대 금지.
   → `ask_user_input` 위젯을 사용해 선택지를 표시하고 사용자 응답을 기다린다.

2. **PROJECT_PATH는 현재 작업 디렉토리 기준으로 설정한다.**
   → bash로 `pwd`를 실행해 현재 경로를 감지한 뒤 `[현재경로]/vue/[PROJECT_NAME]` 으로 설정.
   → 절대로 사용자 홈 루트(`~`, `/home/...`)에 직접 생성하지 않는다.

3. **진행 상태를 실시간으로 표시한다.**
   → 각 레이어 시작 전 현재 단계와 전체 진행률을 출력한다.

4. **shared 레이어는 아래 명시된 파일을 빠짐없이 모두 생성해야 한다.**
   → 생성 완료 후 실제 파일 존재 여부를 `ls`로 검증한다.

---

## 0단계: 모드 선택

`ask_user_input` 위젯으로 사용자에게 모드를 선택하게 한다:

```
질문: "생성 모드를 선택해주세요"
옵션:
  - 빠른 모드 (기본 디자인 토큰 자동 적용, 바로 생성)
  - 상세 모드 (색상/간격/폰트 등 디자인 토큰까지 직접 설정)
```

---

## 1단계: 프로젝트 기본 설정 수집

### 1-1. 현재 작업 디렉토리 감지 (bash 실행)

```bash
CURRENT_DIR=$(pwd)
echo "현재 경로: $CURRENT_DIR"
```

감지한 경로를 사용자에게 확인:
> "현재 경로 `[CURRENT_DIR]` 하위에 `vue/[프로젝트명]/` 폴더를 생성합니다."

### 1-2. 프로젝트명 수집 (텍스트 입력)

> "프로젝트명을 입력해주세요. (예: my-app, aicc-front)"

### 1-3. 패키지 매니저 선택

`ask_user_input` 위젯 사용:
```
질문: "패키지 매니저를 선택해주세요"
옵션: pnpm (권장) | npm | yarn | bun
```

설정값 저장:
```
PROJECT_NAME = [입력값]
PKG_MANAGER  = [선택값]
PROJECT_PATH = [CURRENT_DIR]/vue/[PROJECT_NAME]
```

---

## 2단계: Vue CLI 인터뷰 (⚠️ 3번에 나눠서 순서대로 질문)

**이 단계는 자동 선택 불가. 빠른 모드라도 반드시 사용자 확인을 받는다.**
**`ask_user_input` 한 번에 최대 3개 질문만 가능 → 3회로 나눠서 순차 진행.**

### 2-A차 질문 (ask_user_input — 3개)

```
[질문 1] TypeScript 사용 여부   → Yes (권장) | No
[질문 2] JSX 지원               → No | Yes
[질문 3] Vue Router 추가        → Yes (권장) | No
```

### 2-B차 질문 (ask_user_input — 3개)

```
[질문 4] Pinia 상태관리 추가    → Yes (권장) | No
[질문 5] Vitest 단위 테스트     → No | Yes
[질문 6] E2E 테스트 솔루션      → None | Cypress | Playwright | Nightwatch
```

### 2-C차 질문 (ask_user_input — 3개)

```
[질문 7] 린터 설정              → ESLint (권장) | Oxlint | ESLint + Oxlint | None
[질문 8] Prettier 포맷터        → Yes (권장) | No
[질문 9] Vue DevTools (실험적)  → No | Yes
```

> Oxlint = Rust 기반 고속 린터 (ESLint 대비 50~100배 빠름)
> ESLint+Oxlint 선택 시 Oxlint 먼저 실행, ESLint는 중복 규칙 비활성화 후 보완

CONFIG 저장:
```
CONFIG = {
  typescript, jsx, vueRouter, pinia,
  vitest, e2e, linter, prettier, devtools
}
```

---

## 3단계: 초기 도메인 목록 수집

`ask_user_input` 위젯 (다중선택):
```
질문: "생성할 초기 도메인을 선택해주세요 (나중에 추가 가능)"
옵션: dashboard | user | product | order | 직접 입력
```

입력이 없으면 기본값: `dashboard`

---

## 4단계: 생성 시작 전 설정 확인

```
📋 생성 설정 확인
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
프로젝트명    : [PROJECT_NAME]
생성 경로     : [PROJECT_PATH]
패키지 매니저 : [PKG_MANAGER]
TypeScript    : [O/X]   Vue Router : [O/X]
Pinia         : [O/X]   Vitest     : [O/X]
E2E           : [선택값] 린터       : [선택값]
Prettier      : [O/X]
도메인        : [DOMAINS]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

`ask_user_input`: `생성 시작` | `다시 설정하기`

---

## 5단계: 레이어 순서대로 실행

### 진행 상태 표시 형식

레이어 **시작 직전**:
```
┌─────────────────────────────────────────────┐
│  🔄 [N/7] [레이어명] 레이어 생성 중...        │
│  진행률: ■■■□□□□  N/7 단계                  │
│  생성 예정 파일: XX개                         │
└─────────────────────────────────────────────┘
```

레이어 **완료 직후**:
```
✅ [N/7] [레이어명] 완료 — XX개 파일 생성
```

### 실행 순서

```
[1/7] vue-layer-app    (예상 11개)
[2/7] vue-layer-api    (예상  3개)
[3/7] vue-layer-router (CONFIG.vueRouter=true 시, 예상 3개)
[4/7] vue-layer-store  (CONFIG.pinia=true 시, 예상 2개)
[5/7] vue-layer-shared (예상 21개) ← 아래 체크리스트 전체 생성 필수
[6/7] vue-layer-test   (vitest 또는 e2e 선택 시)
[7/7] vue-layer-domain (도메인마다 반복, 도메인당 3개)
```

공통 전달 컨텍스트:
```
PROJECT_NAME, PROJECT_PATH, PKG_MANAGER, CONFIG, DOMAINS, MODE
```

---

## ⚠️ [5/7] vue-layer-shared 생성 필수 파일 체크리스트

**shared 레이어 실행 시 아래 파일을 빠짐없이 전부 생성해야 한다.**
생성 후 `ls -la` 로 각 디렉터리 파일 존재 여부를 검증한다.

### 디렉터리 생성 (먼저 실행)
```bash
mkdir -p [PROJECT_PATH]/src/shared/{styles,composables,utils}
mkdir -p [PROJECT_PATH]/src/shared/ui/{button,field,input,card,modal,table,feedback}
mkdir -p [PROJECT_PATH]/src/types
```

### styles/ — 3개 파일
```
[ ] src/shared/styles/tokens.css     — CSS 변수 (색상/간격/라운드/그림자/폰트)
[ ] src/shared/styles/reset.css      — 브라우저 기본 스타일 초기화
[ ] src/shared/styles/global.css     — body 폰트, 스크롤바, 전환 효과
```

### ui/button/ — 1개 파일
```
[ ] src/shared/ui/button/BaseButton.vue
    variant(primary|secondary|ghost|danger), size(sm|md|lg), loading, disabled
    loading 시 BaseSpinner 내부 사용, icon-left/icon-right slot
```

### ui/field/ — 1개 파일
```
[ ] src/shared/ui/field/BaseField.vue
    label + 입력 슬롯 + error 메시지 래퍼
    props: label, error, required, hint
    required 시 label에 * 표시, error 시 빨간 메시지
```

### ui/input/ — 3개 파일
```
[ ] src/shared/ui/input/BaseInput.vue
    defineModel<string>(), type(text|password|email|number|search)
    placeholder, disabled, readonly, maxlength, focus/blur emit

[ ] src/shared/ui/input/BaseTextarea.vue
    defineModel<string>(), rows(기본 3), autoResize
    autoResize: watch → nextTick → el.style.height 조정

[ ] src/shared/ui/input/BaseSelect.vue
    defineModel<string|number>()
    options: Array<{label, value, disabled?}>, placeholder
```

### ui/card/ — 1개 파일
```
[ ] src/shared/ui/card/BaseCard.vue
    props: padding(none|sm|md|lg), shadow(boolean)
    slot: default, header(선택), footer(선택)
    디자인 토큰 --radius-base, --shadow-base 적용
```

### ui/modal/ — 1개 파일
```
[ ] src/shared/ui/modal/BaseModal.vue
    defineModel<boolean>('open')
    props: title, size(sm|md|lg), closeOnBackdrop(기본 true)
    Teleport to="body", Escape 키 닫기, fade Transition
    slot: default(본문), footer(액션 버튼)
```

### ui/table/ — 1개 파일
```
[ ] src/shared/ui/table/BaseTable.vue
    props: columns(Array<{key,label,width?,align?}>), rows, loading, emptyText
    loading 시 skeleton 5행, empty 시 BaseEmpty 사용
    slot: cell-[key] 커스터마이징
```

### ui/feedback/ — 3개 파일
```
[ ] src/shared/ui/feedback/BaseSpinner.vue
    props: size(sm|md|lg), color(primary|white|muted)
    SVG 기반 회전 애니메이션 (BaseButton 내부에서도 사용)

[ ] src/shared/ui/feedback/BaseEmpty.vue
    props: message(기본 '데이터가 없습니다.'), description
    slot: icon(기본 SVG 아이콘), action(버튼 등)

[ ] src/shared/ui/feedback/BaseBadge.vue
    props: variant(default|primary|success|warning|danger|info), size(sm|md), dot
    dot=true 시 텍스트 없이 점만 표시
```

### composables/ — 3개 파일
```
[ ] src/shared/composables/useApi.ts
    useApi<T>(apiFn) → { data, isLoading, error, execute }

[ ] src/shared/composables/useToast.ts
    showToast(message, type: success|error|warning|info, duration?)
    전역 reactive 상태로 관리

[ ] src/shared/composables/usePagination.ts
    { page, pageSize, totalPages, totalItems, setPage, nextPage, prevPage, reset }
```

### utils/ — 2개 파일
```
[ ] src/shared/utils/format.ts
    formatDate(date, format?), formatNumber(n), formatBytes(bytes)

[ ] src/shared/utils/validator.ts
    isEmail(v), isRequired(v), isMinLength(v, min), isMaxLength(v, max)
```

### types/ — 2개 파일
```
[ ] src/types/common.ts
    Nullable<T>, Optional<T>, Paginated<T>, ApiResponse<T>, UserProfile

[ ] src/types/api.ts
    LoginRequest, LoginResponse, ErrorResponse
```

### 검증 (생성 완료 후 반드시 실행)
```bash
echo "=== shared 레이어 파일 검증 ==="
find [PROJECT_PATH]/src/shared -name "*.vue" -o -name "*.ts" -o -name "*.css" | sort
find [PROJECT_PATH]/src/types -name "*.ts" | sort
echo "총 파일 수: $(find [PROJECT_PATH]/src/shared [PROJECT_PATH]/src/types -type f | wc -l) (기대값: 21)"
```

21개 미만이면 누락된 파일을 즉시 생성 후 재검증한다.

---

## CONFIG 미선택 시 레이어 처리

| CONFIG 항목 | 처리 |
|------------|------|
| vueRouter=false | [3/7] 건너뜀 |
| pinia=false | [4/7] 건너뜀 |
| vitest+e2e 모두 none | [6/7] 건너뜀 |
| linter=none | 린트 설정 파일 생략 |
| linter=oxlint | oxlintrc.json + vite-plugin-oxlint |
| linter=eslint+oxlint | eslint + eslint-plugin-oxlint 병행 |
| prettier=false | .prettierrc 생략 |
| devtools=false | devtools 패키지 제외 |

---

## 6단계: 완료 출력

```
🎉 프레임워크 생성 완료!
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📁 프로젝트    : [PROJECT_PATH]
📦 총 파일 수  : XX개

생성된 레이어:
  ✅ [1/7] app     — 루트 설정 11개
  ✅ [2/7] api     — HTTP 클라이언트 3개
  ✅ [3/7] router  — 라우팅 3개
  ✅ [4/7] store   — 전역 상태 2개
  ✅ [5/7] shared  — 공통 리소스 21개
             styles 3 | ui 11 | composables 3 | utils 2 | types 2
  ✅ [6/7] test    — 테스트 설정
  ✅ [7/7] domain  — [DOMAINS] 각 3개 파일

🚀 시작하기:
  cd [PROJECT_PATH]
  [PKG_MANAGER] install
  [PKG_MANAGER] dev

📋 다음 단계:
  - .env.example → .env 복사 후 VITE_API_BASE_URL 설정
  - src/features/[domain]/ 에 비즈니스 로직 composable 작성
  - src/widgets/ 에 도메인 전용 UI 블록 작성

🧩 선택적 애드온:
  - 차트 위젯 추가        → "차트 애드온 추가해줘" (vue-chart-addon)
  - 새 도메인 추가        → "user 도메인 추가해줘" (vue-layer-domain)
  - 특정 레이어 재생성    → "api 레이어 다시 만들어줘" (vue-layer-api)
```

---

## 주의사항

- 레이어 실행 중 오류 시 해당 레이어만 재시도 후 계속 진행
- 상세 모드 디자인 토큰 인터뷰는 [5/7] 실행 직전에 완료
- **PROJECT_PATH 생성 전 반드시 `mkdir -p [PROJECT_PATH]` 실행**
- **shared 레이어는 21개 파일 전부 생성 후 ls로 검증 필수**

---
> Source: [limkyulee/front-skills](https://github.com/limkyulee/front-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->

---
name: vue-layer-shared
description: Vue3 프로젝트의 src/shared + src/types 레이어를 생성하는 스킬. 공통 Base UI 컴포넌트 (BaseButton, BaseInput, BaseTextarea, BaseSelect, BaseField, BaseCard, BaseModal, BaseTable), 공통 composable, 유틸, 디자인 토큰 CSS, 공통 타입을 실제 파일로 생성한다. 사용자가 'shared 레이어 만들어줘', 'Base UI 컴포넌트 생성해줘', '공통 컴포넌트 추가', '디자인 토큰 설정해줘', 'BaseButton 만들어줘', '공통 composable 생성', '타입 레이어 추가' 같은 말을 할 때 이 스킬을 사용할 것. vue-framework-gen 오케스트레이터에서 다섯 번째로 실행된다. 기존 Vue3 프로젝트에 shared 레이어만 단독으로 추가할 때도 사용 가능하다. Use when this capability is needed.
metadata:
  author: limkyulee
---

# Vue Layer — Shared (공통 UI + 유틸 + 타입)

src/shared 전체와 src/types를 생성한다.

---

## ⚠️ 핵심 원칙

**총 21개 파일을 빠짐없이 모두 생성한다. 일부만 생성 후 완료 처리 절대 금지.**

생성 완료 후 반드시 아래 체크리스트를 bash로 검증한다:

```bash
# 21개 파일 존재 여부 일괄 확인
SHARED="[PROJECT_PATH]/src/shared"
TYPES="[PROJECT_PATH]/src/types"

files=(
  "$SHARED/styles/tokens.css"
  "$SHARED/styles/reset.css"
  "$SHARED/styles/global.css"
  "$SHARED/ui/button/BaseButton.vue"
  "$SHARED/ui/field/BaseField.vue"
  "$SHARED/ui/input/BaseInput.vue"
  "$SHARED/ui/input/BaseTextarea.vue"
  "$SHARED/ui/input/BaseSelect.vue"
  "$SHARED/ui/card/BaseCard.vue"
  "$SHARED/ui/modal/BaseModal.vue"
  "$SHARED/ui/table/BaseTable.vue"
  "$SHARED/ui/feedback/BaseSpinner.vue"
  "$SHARED/ui/feedback/BaseEmpty.vue"
  "$SHARED/ui/feedback/BaseBadge.vue"
  "$SHARED/composables/useApi.ts"
  "$SHARED/composables/useToast.ts"
  "$SHARED/composables/usePagination.ts"
  "$SHARED/utils/format.ts"
  "$SHARED/utils/validator.ts"
  "$TYPES/common.ts"
  "$TYPES/api.ts"
)

missing=0
for f in "${files[@]}"; do
  if [ ! -f "$f" ]; then
    echo "❌ MISSING: $f"
    missing=$((missing + 1))
  fi
done

if [ $missing -eq 0 ]; then
  echo "✅ 전체 21개 파일 확인 완료"
else
  echo "⚠️ 누락 파일 ${missing}개 — 재생성 필요"
fi
```

누락 파일이 있으면 해당 파일만 재생성한 뒤 검증을 다시 실행한다.

---

## 입력 컨텍스트 확인

```
PROJECT_PATH : 프로젝트 루트 경로
MODE         : quick(기본) | detailed
```

---

## 패키지 설치 체크

```bash
cd [PROJECT_PATH]
if ! node -e "require('@vueuse/core')" 2>/dev/null; then
  [PKG_MANAGER] add @vueuse/core@^10.11.0
fi
```

---

## 생성 파일 목록 (총 21개)

```
src/shared/
├── styles/                            ← 그룹 A (3개)
│   ├── tokens.css
│   ├── reset.css
│   └── global.css
├── ui/
│   ├── feedback/                      ← 그룹 B (3개) — 먼저 생성
│   │   ├── BaseSpinner.vue
│   │   ├── BaseEmpty.vue
│   │   └── BaseBadge.vue
│   ├── field/                         ← 그룹 C (1개)
│   │   └── BaseField.vue
│   ├── input/                         ← 그룹 D (3개)
│   │   ├── BaseInput.vue
│   │   ├── BaseTextarea.vue
│   │   └── BaseSelect.vue
│   ├── button/                        ← 그룹 E (1개)
│   │   └── BaseButton.vue
│   ├── card/                          ← 그룹 F (1개)
│   │   └── BaseCard.vue
│   ├── modal/                         ← 그룹 G (1개)
│   │   └── BaseModal.vue
│   └── table/                         ← 그룹 H (1개)
│       └── BaseTable.vue
├── composables/                       ← 그룹 I (3개)
│   ├── useApi.ts
│   ├── useToast.ts
│   └── usePagination.ts
└── utils/                             ← 그룹 J (2개)
    ├── format.ts
    └── validator.ts

src/types/                             ← 그룹 K (2개)
├── common.ts
└── api.ts
```

---

## bash 실행 원칙

### Step 1: 디렉터리 일괄 생성

```bash
mkdir -p [PROJECT_PATH]/src/shared/{styles,composables,utils} \
         [PROJECT_PATH]/src/shared/ui/{button,field,input,card,modal,table,feedback} \
         [PROJECT_PATH]/src/types
echo "✅ 디렉터리 생성 완료"
```

### Step 2: 그룹별 순차 생성

**반드시 아래 순서를 지킨다** (의존성 순서):
```
그룹 A → 그룹 B → 그룹 C → 그룹 D → 그룹 E → 그룹 F → 그룹 G → 그룹 H → 그룹 I → 그룹 J → 그룹 K
styles → feedback → field → input → button → card → modal → table → composables → utils → types
```

이유:
- feedback(BaseSpinner, BaseEmpty)이 먼저 → BaseButton, BaseTable이 내부에서 참조
- field가 먼저 → input 컴포넌트들이 field와 조합 예시에서 참조

### Step 3: 파일별 생성 (각 파일을 개별 bash 호출로 생성)

각 파일은 `cat > [경로] << 'ENDOFFILE' ... ENDOFFILE` 패턴으로 개별 생성한다.
한 번의 bash 호출에 파일 하나씩 — 너무 길면 오류 발생 가능.

---

## Design Token 설정

**빠른 모드** — 기본 토큰 즉시 적용:
```css
/* src/shared/styles/tokens.css */
:root {
  /* Colors */
  --color-primary-500: #3B82F6;
  --color-primary-600: #2563EB;
  --color-surface:     #FFFFFF;
  --color-border:      #E5E7EB;
  --color-text:        #111827;
  --color-text-muted:  #6B7280;
  --color-success:     #10B981;
  --color-warning:     #F59E0B;
  --color-error:       #EF4444;
  --color-info:        #3B82F6;

  /* Spacing */
  --spacing-unit: 4px;
  --spacing-1: 4px;
  --spacing-2: 8px;
  --spacing-3: 12px;
  --spacing-4: 16px;
  --spacing-6: 24px;
  --spacing-8: 32px;

  /* Radius */
  --radius-sm:   0.25rem;
  --radius-base: 0.5rem;
  --radius-lg:   0.75rem;
  --radius-full: 9999px;

  /* Shadow */
  --shadow-sm:   0 1px 2px rgba(0,0,0,0.05);
  --shadow-base: 0 1px 3px rgba(0,0,0,0.1);
  --shadow-lg:   0 4px 6px rgba(0,0,0,0.1);

  /* Typography */
  --font-sans: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
  --text-sm:   0.875rem;
  --text-base: 1rem;
  --text-lg:   1.125rem;

  /* Transition */
  --transition-fast: 150ms ease;
  --transition-base: 200ms ease;
}
```

**상세 모드** — 생성 전 사용자에게 아래 항목 인터뷰 후 생성:
```
컬러 톤  : 라이트 / 다크 / 시스템
주 색상  : (예: #3B82F6)
밀도     : compact | default | comfortable
라운드   : none | sm | md | lg | full
그림자   : none | soft | medium | strong
폰트     : 기본(Inter) | 커스텀
```

---

## Base UI 컴포넌트 내용 기준

### 그룹 A: 스타일

**`reset.css`** — 브라우저 기본 스타일 초기화 (box-sizing, margin 0, padding 0)

**`global.css`** — body 폰트, 스크롤바 스타일, 기본 전환 효과

---

### 그룹 B: feedback (먼저 생성)

**`BaseSpinner.vue`**
```vue
<script setup lang="ts">
defineProps<{
  size?: 'sm' | 'md' | 'lg'  // 기본: 'md'
  color?: 'primary' | 'white' | 'muted'  // 기본: 'primary'
}>()
</script>
<template>
  <!-- SVG 기반 회전 애니메이션 스피너 -->
  <!-- size에 따라 16px / 24px / 32px -->
  <svg :class="['spinner', `spinner--${size ?? 'md'}`, `spinner--${color ?? 'primary'}`]"
       viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
    <circle cx="12" cy="12" r="10" stroke="currentColor" stroke-width="2" opacity="0.2"/>
    <path d="M12 2a10 10 0 0 1 10 10" stroke="currentColor" stroke-width="2" stroke-linecap="round"/>
  </svg>
</template>
<style scoped>
.spinner { animation: spin 0.8s linear infinite; }
.spinner--sm { width: 16px; height: 16px; }
.spinner--md { width: 24px; height: 24px; }
.spinner--lg { width: 32px; height: 32px; }
.spinner--primary { color: var(--color-primary-500); }
.spinner--white   { color: #ffffff; }
.spinner--muted   { color: var(--color-text-muted); }
@keyframes spin { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }
</style>
```

**`BaseEmpty.vue`**
```vue
<script setup lang="ts">
defineProps<{
  message?: string      // 기본: '데이터가 없습니다.'
  description?: string
}>()
</script>
<template>
  <div class="base-empty">
    <slot name="icon">
      <!-- 기본 빈 박스 아이콘 SVG -->
    </slot>
    <p class="base-empty__message">{{ message ?? '데이터가 없습니다.' }}</p>
    <p v-if="description" class="base-empty__desc">{{ description }}</p>
    <slot name="action" />
  </div>
</template>
<style scoped>
.base-empty { display: flex; flex-direction: column; align-items: center;
              padding: var(--spacing-8); gap: var(--spacing-3); color: var(--color-text-muted); }
.base-empty__message { font-size: var(--text-base); }
.base-empty__desc    { font-size: var(--text-sm); }
</style>
```

**`BaseBadge.vue`**
```vue
<script setup lang="ts">
defineProps<{
  variant?: 'default' | 'primary' | 'success' | 'warning' | 'danger' | 'info'
  size?: 'sm' | 'md'
  dot?: boolean   // 텍스트 없이 점만 표시
}>()
</script>
<template>
  <span :class="['badge', `badge--${variant ?? 'default'}`, `badge--${size ?? 'md'}`, { 'badge--dot': dot }]">
    <slot v-if="!dot" />
  </span>
</template>
<style scoped>
.badge { display: inline-flex; align-items: center; border-radius: var(--radius-full);
         font-size: var(--text-sm); font-weight: 500; }
.badge--sm { padding: 2px 8px; }
.badge--md { padding: 4px 10px; }
.badge--dot { width: 8px; height: 8px; padding: 0; border-radius: 50%; }
.badge--default { background: var(--color-border);   color: var(--color-text); }
.badge--primary { background: var(--color-primary-500); color: #fff; }
.badge--success { background: var(--color-success);  color: #fff; }
.badge--warning { background: var(--color-warning);  color: #fff; }
.badge--danger  { background: var(--color-error);    color: #fff; }
.badge--info    { background: var(--color-info);     color: #fff; }
</style>
```

---

### 그룹 C: field

**`BaseField.vue`**
```vue
<script setup lang="ts">
defineProps<{
  label?: string
  error?: string
  required?: boolean
  hint?: string
}>()
</script>
<template>
  <div class="base-field" :class="{ 'base-field--error': error }">
    <label v-if="label" class="base-field__label">
      {{ label }}<span v-if="required" class="base-field__required">*</span>
    </label>
    <slot />
    <p v-if="hint && !error"  class="base-field__hint">{{ hint }}</p>
    <p v-if="error" class="base-field__error">{{ error }}</p>
  </div>
</template>
<style scoped>
.base-field { display: flex; flex-direction: column; gap: var(--spacing-1); }
.base-field__label    { font-size: var(--text-sm); font-weight: 500; color: var(--color-text); }
.base-field__required { color: var(--color-error); margin-left: 2px; }
.base-field__hint     { font-size: var(--text-sm); color: var(--color-text-muted); }
.base-field__error    { font-size: var(--text-sm); color: var(--color-error); }
</style>
```

---

### 그룹 D: input (3개)

**`BaseInput.vue`**
```vue
<script setup lang="ts">
const model = defineModel<string>()
defineProps<{
  type?: 'text' | 'password' | 'email' | 'number' | 'search'
  placeholder?: string
  disabled?: boolean
  readonly?: boolean
  maxlength?: number
}>()
defineEmits<{ focus: []; blur: [] }>()
</script>
<template>
  <input v-bind="$props" v-model="model" class="base-input"
         @focus="$emit('focus')" @blur="$emit('blur')" />
</template>
<style scoped>
.base-input { width: 100%; padding: var(--spacing-2) var(--spacing-3);
              border: 1px solid var(--color-border); border-radius: var(--radius-base);
              font-size: var(--text-base); color: var(--color-text);
              transition: border-color var(--transition-fast);
              outline: none; background: var(--color-surface); }
.base-input:focus { border-color: var(--color-primary-500); }
.base-input:disabled { opacity: 0.5; cursor: not-allowed; }
</style>
```

**`BaseTextarea.vue`**
```vue
<script setup lang="ts">
import { ref, watch, nextTick } from 'vue'
const model = defineModel<string>()
const props = defineProps<{
  placeholder?: string
  rows?: number        // 기본: 3
  maxlength?: number
  disabled?: boolean
  readonly?: boolean
  autoResize?: boolean
}>()
const el = ref<HTMLTextAreaElement | null>(null)
watch(model, () => {
  if (props.autoResize && el.value) {
    nextTick(() => {
      el.value!.style.height = 'auto'
      el.value!.style.height = el.value!.scrollHeight + 'px'
    })
  }
})
</script>
<template>
  <textarea ref="el" v-model="model" :rows="rows ?? 3" v-bind="$props"
            class="base-textarea" />
</template>
<style scoped>
.base-textarea { width: 100%; padding: var(--spacing-2) var(--spacing-3);
                 border: 1px solid var(--color-border); border-radius: var(--radius-base);
                 font-size: var(--text-base); resize: vertical; outline: none;
                 transition: border-color var(--transition-fast); background: var(--color-surface); }
.base-textarea:focus { border-color: var(--color-primary-500); }
</style>
```

**`BaseSelect.vue`**
```vue
<script setup lang="ts">
const model = defineModel<string | number>()
defineProps<{
  options: Array<{ label: string; value: string | number; disabled?: boolean }>
  placeholder?: string
  disabled?: boolean
}>()
</script>
<template>
  <select v-model="model" class="base-select" :disabled="disabled">
    <option v-if="placeholder" value="" disabled>{{ placeholder }}</option>
    <option v-for="opt in options" :key="opt.value"
            :value="opt.value" :disabled="opt.disabled">{{ opt.label }}</option>
  </select>
</template>
<style scoped>
.base-select { width: 100%; padding: var(--spacing-2) var(--spacing-3);
               border: 1px solid var(--color-border); border-radius: var(--radius-base);
               font-size: var(--text-base); background: var(--color-surface);
               cursor: pointer; outline: none; }
.base-select:focus { border-color: var(--color-primary-500); }
.base-select:disabled { opacity: 0.5; cursor: not-allowed; }
</style>
```

---

### 그룹 E: button

**`BaseButton.vue`**
```vue
<script setup lang="ts">
import BaseSpinner from '@/shared/ui/feedback/BaseSpinner.vue'
defineProps<{
  variant?: 'primary' | 'secondary' | 'ghost' | 'danger'  // 기본: 'primary'
  size?: 'sm' | 'md' | 'lg'                               // 기본: 'md'
  loading?: boolean
  disabled?: boolean
  type?: 'button' | 'submit' | 'reset'                    // 기본: 'button'
}>()
defineEmits<{ click: [MouseEvent] }>()
</script>
<template>
  <button :class="['btn', `btn--${variant ?? 'primary'}`, `btn--${size ?? 'md'}`]"
          :type="type ?? 'button'" :disabled="disabled || loading"
          @click="$emit('click', $event)">
    <slot name="icon-left" />
    <BaseSpinner v-if="loading" size="sm" :color="variant === 'primary' ? 'white' : 'primary'" />
    <slot />
    <slot name="icon-right" />
  </button>
</template>
<style scoped>
.btn { display: inline-flex; align-items: center; gap: var(--spacing-2);
       border: none; border-radius: var(--radius-base); font-weight: 500;
       cursor: pointer; transition: all var(--transition-fast); }
.btn:disabled { opacity: 0.5; cursor: not-allowed; }
.btn--sm { padding: var(--spacing-1) var(--spacing-3); font-size: var(--text-sm); }
.btn--md { padding: var(--spacing-2) var(--spacing-4); font-size: var(--text-base); }
.btn--lg { padding: var(--spacing-3) var(--spacing-6); font-size: var(--text-lg); }
.btn--primary   { background: var(--color-primary-500); color: #fff; }
.btn--primary:hover:not(:disabled)   { background: var(--color-primary-600); }
.btn--secondary { background: var(--color-surface); color: var(--color-text);
                  border: 1px solid var(--color-border); }
.btn--ghost  { background: transparent; color: var(--color-primary-500); }
.btn--danger { background: var(--color-error); color: #fff; }
</style>
```

---

### 그룹 F: card

**`BaseCard.vue`**
```vue
<script setup lang="ts">
defineProps<{
  padding?: 'none' | 'sm' | 'md' | 'lg'  // 기본: 'md'
  shadow?: boolean
}>()
</script>
<template>
  <div :class="['card', `card--padding-${padding ?? 'md'}`, { 'card--shadow': shadow }]">
    <div v-if="$slots.header" class="card__header"><slot name="header" /></div>
    <div class="card__body"><slot /></div>
    <div v-if="$slots.footer" class="card__footer"><slot name="footer" /></div>
  </div>
</template>
<style scoped>
.card { border: 1px solid var(--color-border); border-radius: var(--radius-base);
        background: var(--color-surface); }
.card--shadow { box-shadow: var(--shadow-base); }
.card--padding-none .card__body { padding: 0; }
.card--padding-sm   .card__body { padding: var(--spacing-3); }
.card--padding-md   .card__body { padding: var(--spacing-4); }
.card--padding-lg   .card__body { padding: var(--spacing-6); }
.card__header { padding: var(--spacing-4); border-bottom: 1px solid var(--color-border);
                font-weight: 600; }
.card__footer { padding: var(--spacing-4); border-top: 1px solid var(--color-border); }
</style>
```

---

### 그룹 G: modal

**`BaseModal.vue`**
```vue
<script setup lang="ts">
import { onMounted, onUnmounted } from 'vue'
const open = defineModel<boolean>('open')
const props = defineProps<{
  title?: string
  size?: 'sm' | 'md' | 'lg'        // 기본: 'md'
  closeOnBackdrop?: boolean          // 기본: true
}>()
function onEscape(e: KeyboardEvent) { if (e.key === 'Escape') open.value = false }
onMounted(()  => document.addEventListener('keydown', onEscape))
onUnmounted(() => document.removeEventListener('keydown', onEscape))
</script>
<template>
  <Teleport to="body">
    <Transition name="fade">
      <div v-if="open" class="modal-backdrop"
           @click.self="props.closeOnBackdrop !== false && (open = false)">
        <div :class="['modal', `modal--${size ?? 'md'}`]">
          <div class="modal__header">
            <span>{{ title }}</span>
            <button class="modal__close" @click="open = false">✕</button>
          </div>
          <div class="modal__body"><slot /></div>
          <div v-if="$slots.footer" class="modal__footer"><slot name="footer" /></div>
        </div>
      </div>
    </Transition>
  </Teleport>
</template>
<style scoped>
.modal-backdrop { position: fixed; inset: 0; background: rgba(0,0,0,0.5);
                  display: flex; align-items: center; justify-content: center; z-index: 1000; }
.modal { background: var(--color-surface); border-radius: var(--radius-lg);
         box-shadow: var(--shadow-lg); display: flex; flex-direction: column; max-height: 90vh; }
.modal--sm { width: 400px; }
.modal--md { width: 560px; }
.modal--lg { width: 760px; }
.modal__header { display: flex; justify-content: space-between; align-items: center;
                 padding: var(--spacing-4); border-bottom: 1px solid var(--color-border);
                 font-weight: 600; }
.modal__close  { background: none; border: none; cursor: pointer; font-size: 1.25rem;
                 color: var(--color-text-muted); }
.modal__body   { padding: var(--spacing-4); overflow-y: auto; }
.modal__footer { padding: var(--spacing-4); border-top: 1px solid var(--color-border); }
.fade-enter-active, .fade-leave-active { transition: opacity var(--transition-base); }
.fade-enter-from, .fade-leave-to { opacity: 0; }
</style>
```

---

### 그룹 H: table

**`BaseTable.vue`**
```vue
<script setup lang="ts">
import BaseSpinner from '@/shared/ui/feedback/BaseSpinner.vue'
import BaseEmpty   from '@/shared/ui/feedback/BaseEmpty.vue'
defineProps<{
  columns: Array<{ key: string; label: string; width?: string; align?: 'left' | 'center' | 'right' }>
  rows:    Array<Record<string, unknown>>
  loading?: boolean
  emptyText?: string
}>()
</script>
<template>
  <div class="table-wrapper">
    <table class="base-table">
      <thead>
        <tr>
          <th v-for="col in columns" :key="col.key"
              :style="{ width: col.width, textAlign: col.align ?? 'left' }">
            {{ col.label }}
          </th>
        </tr>
      </thead>
      <tbody>
        <!-- 로딩 스켈레톤 -->
        <template v-if="loading">
          <tr v-for="i in 5" :key="`skeleton-${i}`">
            <td v-for="col in columns" :key="col.key">
              <div class="skeleton" />
            </td>
          </tr>
        </template>
        <!-- 데이터 행 -->
        <template v-else-if="rows.length">
          <tr v-for="(row, i) in rows" :key="i">
            <td v-for="col in columns" :key="col.key"
                :style="{ textAlign: col.align ?? 'left' }">
              <slot :name="`cell-${col.key}`" :row="row" :value="row[col.key]">
                {{ row[col.key] }}
              </slot>
            </td>
          </tr>
        </template>
        <!-- 빈 상태 -->
        <tr v-else>
          <td :colspan="columns.length">
            <BaseEmpty :message="emptyText" />
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
<style scoped>
.table-wrapper { overflow-x: auto; }
.base-table { width: 100%; border-collapse: collapse; font-size: var(--text-sm); }
.base-table th, .base-table td { padding: var(--spacing-3) var(--spacing-4);
                                  border-bottom: 1px solid var(--color-border); }
.base-table th { background: #F9FAFB; font-weight: 600; color: var(--color-text-muted); }
.base-table tbody tr:hover { background: #F9FAFB; }
.skeleton { height: 16px; background: var(--color-border);
            border-radius: var(--radius-sm); animation: pulse 1.5s ease infinite; }
@keyframes pulse { 0%, 100% { opacity: 1; } 50% { opacity: 0.4; } }
</style>
```

---

### 그룹 I: composables

**`useApi.ts`**
```ts
import { ref } from 'vue'
import type { Ref } from 'vue'
import type { AxiosResponse } from 'axios'
import type { ApiResponse } from '@/types/common'

interface UseApiReturn<T> {
  data:      Ref<T | null>
  isLoading: Ref<boolean>
  error:     Ref<string | null>
  execute:   () => Promise<void>
}

export function useApi<T>(
  apiFn: () => Promise<AxiosResponse<ApiResponse<T>>>
): UseApiReturn<T> {
  const data      = ref<T | null>(null) as Ref<T | null>
  const isLoading = ref(false)
  const error     = ref<string | null>(null)

  async function execute(): Promise<void> {
    isLoading.value = true
    error.value     = null
    try {
      const response = await apiFn()
      data.value = response.data.data
    } catch (e: unknown) {
      error.value = e instanceof Error ? e.message : '알 수 없는 오류'
    } finally {
      isLoading.value = false
    }
  }

  return { data, isLoading, error, execute }
}
```

**`useToast.ts`**
```ts
import { reactive } from 'vue'

interface Toast {
  id:       number
  message:  string
  type:     'success' | 'error' | 'warning' | 'info'
  duration: number
}

const state = reactive<{ toasts: Toast[] }>({ toasts: [] })
let nextId = 0

export function useToast() {
  function showToast(
    message: string,
    type: Toast['type'] = 'info',
    duration = 3000
  ): void {
    const id = ++nextId
    state.toasts.push({ id, message, type, duration })
    setTimeout(() => {
      state.toasts = state.toasts.filter(t => t.id !== id)
    }, duration)
  }

  return { toasts: state.toasts, showToast }
}
```

**`usePagination.ts`**
```ts
import { ref, computed } from 'vue'

interface UsePaginationOptions {
  initialPage?:     number  // 기본: 1
  initialPageSize?: number  // 기본: 20
}

export function usePagination(options: UsePaginationOptions = {}) {
  const page       = ref(options.initialPage     ?? 1)
  const pageSize   = ref(options.initialPageSize ?? 20)
  const totalItems = ref(0)

  const totalPages = computed(() =>
    Math.ceil(totalItems.value / pageSize.value)
  )

  function setPage(n: number):   void { page.value = n }
  function nextPage():           void { if (page.value < totalPages.value) page.value++ }
  function prevPage():           void { if (page.value > 1) page.value-- }
  function reset():              void { page.value = 1 }

  return { page, pageSize, totalPages, totalItems, setPage, nextPage, prevPage, reset }
}
```

---

### 그룹 J: utils

**`format.ts`**
```ts
/** 날짜 포맷: 2024-01-15 or 2024.01.15 */
export function formatDate(
  date: string | Date,
  separator: '.' | '-' | '/' = '-'
): string {
  const d = new Date(date)
  const y = d.getFullYear()
  const m = String(d.getMonth() + 1).padStart(2, '0')
  const day = String(d.getDate()).padStart(2, '0')
  return [y, m, day].join(separator)
}

/** 숫자 포맷: 1,234,567 */
export function formatNumber(n: number): string {
  return n.toLocaleString('ko-KR')
}

/** 바이트 → KB/MB/GB */
export function formatBytes(bytes: number): string {
  if (bytes < 1024)        return `${bytes} B`
  if (bytes < 1024 ** 2)   return `${(bytes / 1024).toFixed(1)} KB`
  if (bytes < 1024 ** 3)   return `${(bytes / 1024 ** 2).toFixed(1)} MB`
  return `${(bytes / 1024 ** 3).toFixed(1)} GB`
}

/** 날짜시간 포맷: 2024-01-15 14:30 */
export function formatDateTime(date: string | Date): string {
  const d   = new Date(date)
  const dt  = formatDate(d)
  const hr  = String(d.getHours()).padStart(2, '0')
  const min = String(d.getMinutes()).padStart(2, '0')
  return `${dt} ${hr}:${min}`
}
```

**`validator.ts`**
```ts
/** 이메일 형식 검증 */
export function isEmail(value: string): boolean {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value)
}

/** 필수값 검증 */
export function isRequired(value: unknown): boolean {
  if (typeof value === 'string') return value.trim().length > 0
  return value !== null && value !== undefined
}

/** 최소 길이 검증 */
export function minLength(value: string, min: number): boolean {
  return value.length >= min
}

/** 최대 길이 검증 */
export function maxLength(value: string, max: number): boolean {
  return value.length <= max
}

/** 전화번호 검증 (한국) */
export function isPhoneNumber(value: string): boolean {
  return /^01[0-9]-?\d{3,4}-?\d{4}$/.test(value)
}
```

---

### 그룹 K: types

**`src/types/common.ts`**
```ts
export type Nullable<T>  = T | null
export type Optional<T>  = T | undefined

export interface Paginated<T> {
  items:    T[]
  total:    number
  page:     number
  pageSize: number
}

export interface ApiResponse<T> {
  data:     T
  message?: string
  success:  boolean
}

export interface UserProfile {
  id:         string
  name:       string
  email:      string
  roles:      string[]
  avatarUrl?: string
}
```

**`src/types/api.ts`**
```ts
export interface LoginRequest {
  email:    string
  password: string
}

export interface LoginResponse {
  accessToken:  string
  refreshToken: string
  roles:        string[]
}

export interface ErrorResponse {
  message: string
  error?:  string
  status:  number
}
```

---

## 완료 확인 (검증 포함)

```
✅ [shared] 레이어 생성 완료

파일 수 검증:
  styles      3개  ✅ tokens.css / reset.css / global.css
  feedback    3개  ✅ BaseSpinner / BaseEmpty / BaseBadge
  field       1개  ✅ BaseField
  input       3개  ✅ BaseInput / BaseTextarea / BaseSelect
  button      1개  ✅ BaseButton
  card        1개  ✅ BaseCard
  modal       1개  ✅ BaseModal
  table       1개  ✅ BaseTable
  composables 3개  ✅ useApi / useToast / usePagination
  utils       2개  ✅ format / validator
  types       2개  ✅ common / api
  ─────────────────────────────────
  합계        21개 ✅

⚠️ 누락 파일이 있으면 위 체크리스트를 실행해 재생성 후 다음 단계로 진행
```

다음 실행: `vue-layer-domain`

---
> Source: [limkyulee/front-skills](https://github.com/limkyulee/front-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->

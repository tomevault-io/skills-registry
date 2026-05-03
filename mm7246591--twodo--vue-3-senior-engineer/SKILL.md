---
name: vue-3-senior-engineer
description: 資深 Vue 3 工程實作與審查技能，專注 Vue SFC、Composition API、script setup、響應式系統、props/emits/model、composables，以及 Vue template 與 TwoDo 現行 SCSS/Tailwind 樣式架構的協作。當需要撰寫或重構 Vue 元件、處理狀態與事件流、拆分 composables、實作 Transition/Teleport/Suspense/KeepAlive，或審查 Vue SFC 可維護性時使用。 Use when this capability is needed.
metadata:
  author: mm7246591
---

# Vue 3 資深工程師

## 核心原則

- 新增或重構元件時，優先使用 Vue 3 Composition API 與 `<script setup>`。
- 狀態流保持明確：props down、emits up，共享行為交給 stores 或 composables。
- props、emits、composable returns 與 domain payloads 都要有 TypeScript 型別。
- template 要可讀；業務邏輯放到 computed 或小型 helper。
- 在元件內工作時，避免無關重構。

## SFC 結構

- 新元件或重構後的 SFC 使用 `<script setup lang="ts">`。
- 排列順序：imports、props/emits/models、stores/composables、local refs/computed、watchers、handlers。
- handler 預設使用 `const handleSubmit = () => {}`；只有需要 hoisting 或 overload 時才用 function declaration。
- template 要清楚呈現 loading、empty、error 與 normal states。
- 當一段 template 有獨立狀態、行為或重複結構時，拆成聚焦的子元件。

## Props、Emits 與 Models

- 非簡單 payload 的 props 使用 type 或 interface。
- emits 優先使用 named tuple syntax：

```ts
const emit = defineEmits<{
  submit: [payload: FormPayload];
  cancel: [];
}>();
```

- `defineModel` 只用在真正受控的 form/input-like component。
- 不要 mutate props；需要可編輯資料時，從 props 派生 local state。

## Reactivity

- primitive value 與小型 mutable object 使用 `ref`。
- derived display labels、class state、permissions、filtered lists 使用 `computed`。
- `watch` 只用於 side effects、外部同步，或 route/store 變化。
- watcher 要窄且明確；除非資料形狀真的需要，不要用 deep watch。
- event listeners、subscriptions、intervals 與外部 handles 要在 `onBeforeUnmount` 清理。

## Template 與樣式協作

- 寫 class 時遵守 TwoDo 樣式架構：
  - 元件專屬樣式寫在 Vue template 的 Tailwind utilities
  - spacing 與 size 使用實際 px arbitrary class，例如 `px-[20px]`、`gap-[16px]`、`h-[44px]`
  - 不要引入 `px-5`、`gap-4`、`h-11`、`text-sm` 這類 Tailwind scale 縮寫
  - 顏色使用 `text-[var(--app-text)]` 這類語意 CSS variables
- 動態且重複的 class state 可用 local computed class arrays。
- 只有 selector、pseudo-elements、第三方內部結構不適合 template class 時，才用 `<style scoped lang="scss">`。
- Vue refactor 時，不要把 page-specific 樣式加到全域 SCSS。

## Transitions 與 Overlays

- `Transition` 使用明確 class names 或 inline Tailwind transition classes。
- modal、popover、toast、overlay 這類需要脫離 layout stacking 的 UI 使用 `Teleport`。
- 修改 overlay 時保留 focus management、keyboard behavior 與 ARIA labels。
- animated UI 優先維持穩定 DOM 結構，避免造成 layout 不可預期跳動。

## 審查清單

- Props、emits、models 都有型別。
- Template 沒有塞進過重 inline computation。
- Derived labels/classes 使用 computed。
- 必要時包含 loading、empty、disabled、error states。
- Event listeners/subscriptions 有清理。
- 元件樣式符合目前 TwoDo Tailwind/SCSS 規則。
- 修改後 build 或相關測試通過。

---
> Source: [mm7246591/TwoDo](https://github.com/mm7246591/TwoDo) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-29 -->

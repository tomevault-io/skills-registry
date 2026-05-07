---
name: vue-best-practices
description: Vue.js 任務必須使用。強烈建議以 Composition API 搭配 `<script setup>` 與 TypeScript 作為標準做法。涵蓋 Vue 3、SSR、Volar、vue-tsc。任何 Vue、.vue 檔案、Vue Router、Pinia 或 Vite with Vue 工作都要載入。除非專案明確要求 Options API，否則一律使用 Composition API。 Use when this capability is needed.
metadata:
  author: mm7246591
---

# Vue 最佳實務工作流程

將此 Skill 作為指令集使用。除非使用者明確要求不同順序，否則依照流程執行。

## 核心原則
- **保持狀態可預期：** 單一資料來源，其餘資料都由它推導。
- **讓資料流明確：** 多數情境採用 props down、events up。
- **偏好小而專注的元件：** 更容易測試、重用與維護。
- **避免不必要的重新 render：** 謹慎使用 computed properties 與 watchers。
- **可讀性很重要：** 撰寫清楚、能自我說明的程式碼。

## 1) 寫程式前確認架構（必須）

- 預設技術棧：Vue 3 + Composition API + `<script setup lang="ts">`。
- 如果專案明確使用 Options API，且 `vue-options-api-best-practices` Skill 可用，請載入它。
- 如果專案明確使用 JSX，且 `vue-jsx-best-practices` Skill 可用，請載入它。

### 1.1 必讀核心參考（必須）

- 實作任何 Vue 任務前，務必閱讀並套用這些核心參考：
  - `references/reactivity.md`
  - `references/sfc.md`
  - `references/component-data-flow.md`
  - `references/composables.md`
- 在整個任務期間都要讓這些參考維持於工作脈絡中，而不是只在特定問題出現時才查看。

### 1.2 寫程式前規劃元件邊界（必須）

對任何非瑣碎功能，在實作前先建立簡短的 component map。

- 用一句話定義每個 component 的單一責任。
- 預設讓 entry/root 與 route-level view component 作為 composition surface。
- 除非任務刻意是極小型單檔 demo，否則將 feature UI 與 feature logic 移出 entry/root/view component。
- 在 map 中定義每個 child component 的 props/emits contract。
- 新增超過一個 component 時，優先使用 feature folder layout（`components/<feature>/...`、`composables/use<Feature>.ts`）。

## 2) 套用必要的 Vue 基礎（必須）

這些是必要且必須掌握的基礎。每個 Vue 任務都要使用 `1.1` 已載入的核心參考，並套用以下內容。

### 響應性

- `1.1` 必讀參考：[reactivity](references/reactivity.md)
- 保持 source state 最小化（`ref`/`reactive`），能由 `computed` 推導的都由 `computed` 推導。
- 需要 side effect 時才使用 watchers。
- 避免在 template 中重複計算昂貴邏輯。

### SFC 結構與 template 安全

- `1.1` 必讀參考：[sfc](references/sfc.md)
- SFC 區塊順序維持：`<script>` → `<template>` → `<style>`。
- 保持 SFC 責任聚焦；拆分大型 component。
- 保持 template 宣告式；將分支與推導移到 script。
- 套用 Vue template 安全規則（`v-html`、list rendering、conditional rendering choices）。

### 保持 component 聚焦

當 component 有**超過一個明確責任**時就拆分（例如 data orchestration + UI，或多個彼此獨立的 UI section）。

- 優先使用**較小的 components + composables**，避免單一「mega component」。
- 將 **UI sections** 移入 child components（props in、events out）。
- 將 **state/side effects** 移入 composables（`useXxx()`）。

套用客觀拆分觸發條件。若**任一**條件成立，就拆分 component：

- 它同時負責 orchestration/state，以及多個 section 的大量 presentational markup。
- 它有 3 個以上不同 UI section（例如 form、filters、list、footer/status）。
- 某個 template block 重複出現，或可變成可重用內容（item rows、cards、list entries）。

Entry/root 與 route view 規則：

- 保持 entry/root 與 route view components 精簡：app shell/layout、provider wiring、feature composition。
- 當 feature 包含獨立部分時，不要把完整 feature implementation 放在 entry/root/view components。
- 對 CRUD/list features（todo、table、catalog、inbox），至少拆成：
  - feature container component
  - input/form component
  - list（and/or item）component
  - footer/actions 或 filter/status component
- 只有非常小的一次性 demo 才允許單檔實作；若選擇單檔，需明確說明為何不需要拆分。

### Component 資料流

- `1.1` 必讀參考：[component-data-flow](references/component-data-flow.md)
- 以 props down、events up 作為主要模型。
- 只有真正雙向 component contract 才使用 `v-model`。
- 只有深層樹狀依賴或 shared context 才使用 provide/inject。
- 視需要使用 `defineProps`、`defineEmits` 與 `InjectionKey`，讓 contract 明確且有型別。

### Composables

- `1.1` 必讀參考：[composables](references/composables.md)
- 當邏輯可重用、具備 state，或 side effect 較重時，抽到 composables。
- 保持 composable API 小、具備型別且可預期。
- 將 feature logic 與 presentational components 分離。

## 3) 只有需求需要時才考慮可選功能

### 3.1 標準可選功能

不要預設加入這些功能。只有需求存在時才載入對應參考。

- Slots：parent 需要控制 child content/layout -> [component-slots](references/component-slots.md)
- Fallthrough attributes：wrapper/base components 必須安全轉發 attrs/events -> [component-fallthrough-attrs](references/component-fallthrough-attrs.md)
- 內建 component `<KeepAlive>`：用於 stateful view caching -> [component-keep-alive](references/component-keep-alive.md)
- 內建 component `<Teleport>`：用於 overlays/portals -> [component-teleport](references/component-teleport.md)
- 內建 component `<Suspense>`：用於 async subtree fallback boundaries -> [component-suspense](references/component-suspense.md)
- Animation 相關功能：選擇符合所需 motion behavior 的最簡單方法。
  - 內建 component `<Transition>`：用於 enter/leave effects -> [transition](references/component-transition.md)
  - 內建 component `<TransitionGroup>`：用於 animated list mutations -> [transition-group](references/component-transition-group.md)
  - Class-based animation：用於非 enter/leave effects -> [animation-class-based-technique](references/animation-class-based-technique.md)
  - State-driven animation：用於由使用者輸入驅動的 animation -> [animation-state-driven-technique](references/animation-state-driven-technique.md)

### 3.2 較少見的可選功能

只有明確產品或技術需求時才使用。

- Directives：行為與 DOM 強相關，且不適合用 composable/component 表達 -> [directives](references/directives.md)
- Async components：大型或少用 UI 應 lazy loaded -> [component-async](references/component-async.md)
- Render functions：只有 templates 無法表達需求時才使用 -> [render-functions](references/render-functions.md)
- Plugins：行為必須 app-wide install 時使用 -> [plugins](references/plugins.md)
- State management patterns：app-wide shared state 跨越 feature boundaries -> [state-management](references/state-management.md)

## 4) 行為正確後再做效能最佳化

效能工作是功能完成後的檢查步驟。在核心行為實作並驗證前，不要先做最佳化。

- 大型列表 render 瓶頸 -> [perf-virtualize-large-lists](references/perf-virtualize-large-lists.md)
- 靜態 subtree 不必要地重新 render -> [perf-v-once-v-memo-directives](references/perf-v-once-v-memo-directives.md)
- 熱門列表路徑中過度抽象 -> [perf-avoid-component-abstraction-in-lists](references/perf-avoid-component-abstraction-in-lists.md)
- 昂貴更新觸發太頻繁 -> [updated-hook-performance](references/updated-hook-performance.md)

## 5) 結束前最終自我檢查

- 核心行為可運作且符合需求。
- 所有必讀參考都已閱讀並套用。
- Reactivity model 最小且可預期。
- 遵守 SFC 結構與 template 規則。
- Components 聚焦且拆分良好，必要時已拆分。
- 除非有明確小型 demo 例外，entry/root 與 route view components 仍維持 composition surfaces。
- Component 拆分決策明確且站得住腳（責任邊界清楚）。
- Data flow contracts 明確且有型別。
- 在重用/複雜度足以支持時使用 composables。
- 適用時已將 state/side effects 移入 composables。
- Optional features 只在需求要求時使用。
- Performance changes 只在功能完成後套用。

---
> Source: [mm7246591/TwoDo](https://github.com/mm7246591/TwoDo) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-07 -->

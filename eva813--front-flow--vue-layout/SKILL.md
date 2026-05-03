---
name: vue-layout
description: Vue 3 + SCSS 切版技能（切版 Agent）。當使用者提供 Figma 連結或設計稿 spec，需要產出 Vue 3 純展示元件（Dumb Components）時使用。觸發情境包含：「幫我切版」、「根據 Figma 產出元件」、「根據 spec 建立 UI」、「幫我建 Vue 元件」、「切這個畫面」、有提到 Figma URL 或 Node ID 加上 Vue/元件/切版等字詞。僅產出 props/emits 介面的純 UI 結構，不含 API 呼叫或業務邏輯。 Use when this capability is needed.
metadata:
  author: Eva813
---

# vue-layout Skill（切版 Agent）

## 1) 目標

根據 `spec.md` 與 Figma 設計稿，建立 Vue 3 + SCSS 的純展示元件，輸出可被上層容器直接串接的 UI 結構。

---

## 2) 角色定義

- **你是**：前端切版 Agent（Vue 3 + SCSS）
- **你的職責**：UI 結構、視覺樣式、語意化 HTML、props/emits 介面
- **你不做**：API 呼叫、資料 mapping、商業邏輯、狀態管理、跨頁流程

---

## 3) 執行流程

### Step 1：讀取輸入

收集以下資訊，若缺少則主動詢問使用者：

- **Figma 連結**（必要）：完整 URL 或 Node ID
- **spec.md**（選填）：功能說明、互動規格、欄位定義
- **元件命名規則**（選填）：若 repo 有慣例，請依循

若使用者已提供 Figma URL，使用 Framelink MCP for Figma 讀取設計稿（詳見 Section 4）。

---

### Step 2：解讀 Figma 設計稿

使用 MCP 工具讀取 Figma 節點，提取以下資訊：

| 類型 | 說明 |
|------|------|
| 版面結構 | 父子節點關係、Auto Layout 方向與間距 |
| 色彩 | Fill、Stroke、背景色（轉為 CSS 變數或 SCSS 變數）|
| 字級 | Font size、weight、line-height |
| 間距 | Padding、Margin、Gap |
| 互動點 | Button、Input、Select、Tab 等可互動元素 |

輸出 Figma 解讀摘要給使用者確認：

```
Figma 解讀完成：
- 元件名稱：{ComponentName}
- 主要區塊：{sections}
- 互動點：{interactive_elements}
- 斷點：{breakpoints}
```

---

### Step 3：建立元件架構

確認解讀摘要後，規劃元件拆分：

```
{ComponentName}/
├── index.vue          # 主元件
├── {SubComponent}.vue # 子元件（視需要）
└── types.ts           # Props / Emits 型別定義
```

命名規則：
- 以功能語意命名（ClaimCard、PolicyTable）
- 避免視覺導向名稱（BlueBox、LeftPanel）
- 遵循 PascalCase（SFC 檔名與元件名稱一致）

---

### Step 4：實作元件

#### 4.1 HTML 結構（語意優先）

先完成語意化 HTML，再處理樣式，巢狀不超過 4 層。

```vue
<template>
  <section class="claim-card">
    <header class="claim-card__header">
      <slot name="header" />
    </header>
    <div class="claim-card__body">
      <!-- content -->
    </div>
  </section>
</template>
```

#### 4.2 Props / Emits 介面（types.ts）

```typescript
export interface ClaimCardProps {
  title: string
  status: 'pending' | 'approved' | 'rejected'
  amount: number
  isLoading?: boolean
}

export type ClaimCardEmits = {
  (e: 'click:detail', id: string): void
  (e: 'click:cancel', id: string): void
}
```

Script setup 寫法：

```vue
<script setup lang="ts">
import type { ClaimCardProps, ClaimCardEmits } from './types'

const props = withDefaults(defineProps<ClaimCardProps>(), {
  isLoading: false
})

const emit = defineEmits<ClaimCardEmits>()
</script>
```

#### 4.3 SCSS 樣式（Mobile First）

```vue
<style lang="scss" scoped>
.claim-card {
  display: flex;
  flex-direction: column;
  padding: 16px;

  &__header {
    font-size: 16px;
    font-weight: 600;
  }

  @media (min-width: 768px) {
    padding: 24px;
  }

  @media (min-width: 1280px) {
    flex-direction: row;
  }
}
</style>
```

---

### Step 5：斷點 B — 視覺預覽與交接

完成切版後輸出：

```
元件切版完成，請查看本地預覽。
建議命令：npm run storybook（若未建置 Storybook，改用 npm run dev 指定路由）

確認後請輸入 Continue，以交接 UI 元件路徑給下一個 Agent。
```

同時輸出 Handoff Payload：

```json
{
  "component_paths": [
    "src/components/ClaimCard/index.vue",
    "src/components/ClaimCard/types.ts"
  ],
  "figma_node_ids": ["123:456"],
  "spec_version": "spec.md v1.0",
  "props_summary": { "ClaimCard": ["title", "status", "amount", "isLoading"] },
  "emits_summary": { "ClaimCard": ["click:detail", "click:cancel"] }
}
```

---

## 4) MCP 工具使用指南

### Framelink MCP for Figma

從 Figma URL 解析 fileId：
```
https://www.figma.com/file/{fileId}/...?node-id={nodeId}
```

常用操作：
- `get_node(fileId, nodeId)` — 取得節點結構與樣式
- `get_styles(fileId)` — 取得全域樣式變數
- `get_components(fileId)` — 取得元件列表

### mcp-fs

建立與修改元件檔案時使用。

---

## 5) 禁止事項

| 禁止 | 替代方案 |
|------|----------|
| fetch() / axios | 改用 prop 傳入資料 |
| useStore() / Pinia action | 改用 emit 向上通知 |
| router.push() | 改用 emit 通知父層導頁 |
| v-if 加商業判斷 | 改用 computed prop |
| API response mapping | 由父層容器處理後傳入 |

---

## 6) 完成定義（DoD）

- UI 與 Figma 結構/視覺一致（允許合理工程化微調）
- 所有互動點都有對應 props 或 emits
- 無 API 呼叫與業務邏輯混入
- SCSS 採 Mobile First 撰寫
- 巢狀不超過 4 層
- 輸出 Handoff Payload 供下游 Agent 使用

---

## 7) 常見模式

詳見 references/component-patterns.md（Table、Form、Card、Modal 的切版範本）

---
> Source: [Eva813/front-flow](https://github.com/Eva813/front-flow) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->

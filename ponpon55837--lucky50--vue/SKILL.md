---
name: vue
description: 編輯 .vue 組件、建立 Vue 3 組件、撰寫 composables 或測試 Vue 代碼時使用 - 提供 Composition API 模式、props/emits 最佳實踐、VueUse 整合、reactive destructuring 指引 Use when this capability is needed.
metadata:
  author: ponpon55837
---

# Vue 3 開發指南

Vue 3 Composition API 開發模式、組件架構與測試實踐的完整參考文件。

**當前穩定版本：** Vue 3.5+ 帶來顯著的效能提升（記憶體使用減少 56%、陣列追蹤速度提升 10 倍）、全新的 SSR 功能，以及更好的開發體驗。

## 📋 概述

本 skill 採用漸進式參考系統設計，專為 Vue 3 專案打造。根據當前任務**按需載入**相關檔案，有效減少 context 用量（基礎檔案約 250 tokens，子檔案各約 500-1500 tokens）。

## 🎯 使用時機

**在以下情況使用本 skill：**

- 撰寫或編輯 `.vue` 組件檔案
- 建立 composables（`use*` 開頭的函數）
- 開發客戶端工具函數（utilities）
- 撰寫 Vue 組件或 composables 的測試

**若需要 Nuxt 特定功能，請使用 `nuxt` skill：**

- 伺服器路由、API 端點
- 檔案式路由、中介層（middleware）
- Nuxt 框架特有的開發模式

**其他相關 skills：**

- **UI 組件樣式：** 使用 `nuxt-ui` skill
- **無障礙組件：** 使用 `reka-ui` skill
- **VueUse 函式庫：** 使用 `vueuse` skill

## 🗂️ 快速索引

| 開發工作                      | 載入檔案                   |
| ----------------------------- | -------------------------- |
| `components/` 目錄下的 `.vue` | references/components.md   |
| `composables/` 目錄下的檔案   | references/composables.md  |
| `utils/` 目錄下的檔案         | references/utils-client.md |
| `.spec.ts` 或 `.test.ts` 測試 | references/testing.md      |

## 📂 模組化載入策略

**根據工作內容選擇性載入相關檔案：**

- 組件開發 → [references/components.md](references/components.md)
- Composable 開發 → [references/composables.md](references/composables.md)
- 工具函數開發 → [references/utils-client.md](references/utils-client.md)
- 測試撰寫 → [references/testing.md](references/testing.md)

**⚠️ 重要提醒：** 請勿一次載入所有檔案，這會浪費 context 在不相關的內容上。

## 📚 參考文件說明

**[references/components.md](references/components.md)** - Props reactive destructuring、emits 模式、defineModel 雙向綁定、slots 簡寫語法

**[references/composables.md](references/composables.md)** - Composition API 結構、VueUse 整合、生命週期鉤子、非同步處理模式

**[references/utils-client.md](references/utils-client.md)** - 純函數設計、格式化函數、驗證器、轉換器、何時不該使用 utils

**[references/testing.md](references/testing.md)** - Vitest + @vue/test-utils 測試框架、組件測試、composable 測試、模擬（mocking）模式

## 💡 範例說明

本 skill 原始版本包含範例檔案於 `resources/examples/` 目錄：

- `component-example.vue` - 完整組件範例（包含所有模式）
- `composable-example.ts` - 可重用的 composition 函數範例

**注意：** Lucky50 專案可參考 `src/components/` 和 `src/composables/` 目錄下的實際代碼作為範例。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ponpon55837) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

---
name: doc-generator-strategy
description: Use when generating or improving user manuals or documentation strategies. Focuses on Code-First Analysis, Process Restoration, and Structured Standards.
metadata:
  author: samchang72
---

# User Manual Generation Strategy (改進使用者手冊生成的策略)

## Overview

此技能旨在解決「如何改善生成內容」的問題，針對使用者手冊 (User Manual) 與技術文件的生成提供標準化策略。核心理念是摒棄猜測，改以**程式碼為單一真理來源 (Code as Single Source of Truth)**，並透過結構化的方式還原應用程式的全貌，確保文件的精確性與完整性。

## When to Use

- 當需要為 Web 應用程式撰寫、更新或重構使用者手冊時。
- 當發現生成的文件內容有缺漏、不準確或與實際行為不符時。
- 當需要制定文件寫作的標準作業程序 (SOP) 時。
- 當需要分析現有專案以產出系統功能清單時。

## Core Strategies (重點策略)

### 1. 程式碼優先分析 (Code-First Analysis)
**原則**：從源頭抓取精確規則，而非僅憑猜測或過時的文件。

*   **前端驗證邏輯 (Frontend Validation Logic)**：
    *   **Validation Factories/Schemas**：這是**最關鍵**的業務規則來源。檢查 `src/factory`、`validation.js` 或 Component 內的 `rules` (如 Zod, Yup, VeeValidate)。
    *   **具體數值**：從中提取「優先順序需在 40-60 之間」、「預算不可為負」等精確限制，這些往往不在資料庫 Schema 中。
*   **Schema 與型別定義**：
    *   分析資料庫 Schema (如 Prisma schema, SQL) 或 API 合約 (Swagger/OpenAPI) 以獲取準確的欄位名稱、資料類型。
    *   分析 TypeScript Interfaces/Types 了解資料結構。
*   **設定與常數 (Config & Constants)**：
    *   檢查設定檔與常數定義，確認系統預設值、列舉值 (Enums) 的含義及功能開關 (Feature Flags)。

### 2. 流程還原 (Process Restoration)
**原則**：透過路由反推完整網站地圖，確保零遺漏。

*   **路由與權限反向工程 (Route & Guard Analysis)**：
    *   從路由檔案 (如 `src/router/index.js`, `routes.ts`) 反推頁面結構。
    *   **權限守衛 (Route Guards)**：檢查 `beforeEnter` 或路由 Middleware (e.g., `checkPermission('create', 'campaign')`)，精確定義每個功能對應的角色權限，而非通用描述。
*   **網站地圖 (Sitemap) 重建**：
    *   根據路由層級建立完整的網站地圖，作為文件目錄的骨架。
    *   確保每一個可訪問的路由都有對應的文件章節。
*   **導航元件**：
    *   分析 Sidebar/Navbar 確認實際入口與層級關係。

### 3. 結構化標準 (Structured Standards)
**原則**：建立統一的寫作模板，確保深度一致。

*   **統一寫作模板**：每個功能章節應包含以下標準區塊：
    *   **功能概述 (Overview)**：一句話說明此功能的目的與價值。
    *   **前置條件 (Prerequisites)**：根據 **Route Guards** 分析所需的具體權限 (e.g., `create:campaign`)。
    *   **操作指引 (Step-by-Step Guide)**：條列式操作步驟。
    *   **欄位說明 (Field Reference)**：基於 **Frontend Validation** 的詳細欄位定義（名稱、說明、必填、格式限制、預設值）。
    *   **常見問題與邊界情況 (FAQ & Edge Cases)**：從驗證邏輯中的 `errorMessage` 反推使用者可能遇到的錯誤。

## Checklist (執行檢核表)

- [ ] **Validation Factory Check**: 是否已找到並分析前端表單驗證邏輯檔案？
- [ ] **Router Guard Check**: 是否已分析路由檔案中的權限守衛設定？
- [ ] **Source Check**: 是否已開啟並分析 schema、types 或相關程式碼檔案？
- [ ] **Map Check**: 是否已列出所有相關路由並建立 Sitemap？
- [ ] **Template Check**: 產出的內容是否符合「結構化標準」的模板要求？

## Example Workflow

1.  **Phase 1: Analysis**
    *   讀取 `src/router/index.js` 列出所有頁面與 `checkPermission` 設定。
    *   讀取 `src/factory/campaignValidation.js` (或類似檔案) 提取業務規則。
2.  **Phase 2: Structure**
    *   根據路由與權限建立 Markdown 文件大綱。
3.  **Phase 3: Content Generation (Per Feature)**
    *   開啟該功能的 UI 元件檔案 (e.g., `CampaignCreate.vue`)。
    *   對照 Validation Factory 填入「欄位說明」中的格式限制 (e.g., "每日預算需 > 1000")。
    *   填入「前置條件」中的權限要求。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/samchang72) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

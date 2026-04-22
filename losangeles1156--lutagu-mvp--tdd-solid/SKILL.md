---
name: tdd-solid-development
description: > Use when this capability is needed.
metadata:
  author: losangeles1156
---

# TDD & SOLID Development Guide

本 Skill 指導 Agent 遵循測試驅動開發 (TDD) 與 SOLID 原則進行高品質程式碼交付。

## 🎯 核心原則 (Core Principles)

1.  **No Code Without Tests**: 沒有失敗的測試前，不寫任何一行 Production Code。
2.  **Simple Design**: 只寫剛好能通過測試的代碼，不要過度設計 (YAGNI)。
3.  **Refactor Mercilessly**: 在綠燈 (測試通過) 後，無情地重構，確保符合 SOLID。

## 🔄 TDD 循環流程 (The Loop)

在執行任何程式碼變更時，必須嚴格遵守以下三階段循環：

### Phase 1: 🔴 RED (Write a Failing Test)
1.  **理解需求**: 分析用戶需求，決定要測試的行為 (Behavior)。
2.  **建立/修改測試檔**:
    *   測試檔案與原始碼並列，命名為 `*.test.ts`。
    *   使用 `node --test` 原生模組 (專案使用 `npm test` 執行)。
3.  **撰寫斷言 (Assert)**:
    *   描述預期輸入與輸出。
    *   確保測試目前會**失敗** (因為功能尚未實作)。
4.  **驗證失敗**: 執行 `npm test`，確認看到預期的紅燈錯誤。

### Phase 2: 🟢 GREEN (Make It Pass)
1.  **實作最小代碼**:
    *   只寫能讓測試通過的「最少」代碼。
    *   可以使用 Hardcode、簡陋的邏輯，先求有再求好。
2.  **驗證通過**: 執行 `npm test`，確認看到綠燈。

### Phase 3: 🔵 REFACTOR (Clean & SOLIDify)
1.  **優化代碼結構**: 移除重複、優化變數命名、提取方法。
2.  **SOLID 審查**: 對照下方的 SOLID 檢查表進行審視。
3.  **回歸測試**: 每次修改後，立即跑 `npm test` 確保沒壞。

---

## 🛡️ SOLID 原則檢查表 (SOLID Checklist)

在 **Refactor** 階段，必須逐一檢查：

### 1. SRP (Single Responsibility Principle) - 單一職責
*   [ ] 這個 Class/Function 是否只有**一個修改的理由**？
*   [ ] 是否把「業務邏輯」與「UI 渲染」或「資料存取」混在一起？
*   *Action*: 若發現多重職責，請將其拆分為不同的類別或函數。

### 2. OCP (Open/Closed Principle) - 開閉原則
*   [ ] 新增功能時，是「新增代碼」還是「修改舊代碼」？
*   [ ] 是否使用了過多的 `if-else` 或 `switch` 來判斷類型？
*   *Action*: 使用多型 (Polymorphism) 或策略模式 (Strategy Pattern) 來取代條件判斷。

### 3. LSP (Liskov Substitution Principle) - 里氏替換
*   [ ] 子類別是否能完全替代父類別而不報錯？
*   [ ] 子類別是否拋出了父類別沒有定義的異常？
*   *Action*: 確保繼承關係是 `IS-A` 且行為一致，否則改用組合 (Composition)。

### 4. ISP (Interface Segregation Principle) - 介面隔離
*   [ ] 呼叫者是否依賴了它不需要的方法？
*   [ ] 介面是否過於肥大 (Fat Interface)？
*   *Action*: 將大介面拆分為多個小介面 (Role Interfaces)。

### 5. DIP (Dependency Inversion Principle) - 依賴反轉
*   [ ] 高層模組是否直接依賴低層模組？(例如 Service 直接 new Repository)
*   [ ] 是否依賴了具體實作而非抽象介面？
*   *Action*: 使用依賴注入 (Dependency Injection)，依賴 Interface 而非 Class。

---

## 🛠️ 專案測試指南 (Project Testing Setup)

本專案使用 **Node.js Native Test Runner**。

*   **執行測試**: `npm test` (會遞迴執行 `src` 下所有 `*.test.ts`)
*   **測試模板範例**:
    ```typescript
    import { describe, it } from 'node:test';
    import assert from 'node:assert';
    import { myService } from './myService';

    describe('MyService', () => {
      it('should return correct value given input', () => {
        // Arrange
        const input = 1;

        // Act
        const result = myService(input);

        // Assert
        assert.strictEqual(result, 2);
      });
    });
    ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/losangeles1156) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

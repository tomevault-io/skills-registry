---
name: backend-architect
description: 建立強型別、高效能且符合分層架構的後端系統。 Use when this capability is needed.
metadata:
  author: samchang72
---

# Skill: Backend Architect

## 核心任務
建立強型別、高效能且符合分層架構的後端系統。

## 技術堆疊
- **語言**：強制使用 **TypeScript**。
- **驗證**：使用 **Zod** 進行強型別驗證。
- **日誌**：使用 **Pino** 進行結構化日誌，禁用 `console.log`。

## API 規範
- **統一格式**：`{ status: 'success'|'error', data: T, message?: string }`。
- **分頁**：列表必須包含 pagination metadata。

## 資料庫效能
- **嚴禁 N+1 問題**，必須使用 JOIN 或 Batch Loading。
- **禁止 `SELECT *`**，必須明確指定欄位。
- **索引**：Foreign Key、ORDER BY 與 WHERE 常用欄位必須建立索引。
- **大數據分頁**：禁止 OFFSET，採用 Cursor-based 或 Keyset Pagination。

## 分層架構 (Layered Architecture)
1. **Controllers**: 處理 HTTP 請求與回應。
2. **Services**: 封裝核心業務邏輯。
3. **Repositories**: 負責資料庫存取。
4. **Models**: 定義數據模型與類型。

## 安全
- 配置 Helmet、CORS 與 Rate Limiting；嚴禁硬編碼 Secret Keys。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/samchang72) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

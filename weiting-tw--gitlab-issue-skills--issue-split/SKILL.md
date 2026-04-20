---
name: issue-split
description: 將大型 GitLab Issue 拆分為多個子 Issue，AI 語義理解自動識別子任務並建立關聯。當使用者說「拆分 issue」、「split issue」、「拆 issue」、「分拆」時觸發。 Use when this capability is needed.
metadata:
  author: weiting-tw
---

# Issue Split

智慧拆分大型 GitLab Issue 為多個子 Issue。AI 分析 Issue 內容和討論串，識別獨立子任務，建立 Issue 間的關聯。

## 使用方式

### 基本用法（指定 Issue IID）
```
/issue-split #123
```

### 指定專案
```
/issue-split --project=api #45
```

### 從 URL
```
/issue-split https://gitlab.example.com/BizForm/Home/-/issues/123
```

參數說明：
- `#IID` 或 URL（必要）：要拆分的 Issue
- `--project`（可選）：專案 key，預設為 defaultProjectPath

## 前置條件

需要 `.issue-config.json` 存在。按以下優先順序搜尋：
1. 專案目錄: `./.issue-config.json`
2. 使用者目錄: `~/.claude/.issue-config.json`

若均不存在，提示：「找不到 .issue-config.json，請先執行 /issue-init 建立設定。」

## 工作流程

### 1. 讀取設定

從搜尋到的 `.issue-config.json` 讀取（專案層級優先於使用者層級）：
- `gitlab.defaultProjectPath`（或 `--project` 覆寫）
- `split.maxSubIssues`（預設 8）
- `split.inheritLabels`（預設 true）
- `split.linkType`（預設 "relates_to"）
- `defaults.labels`

### 2. 取得原始 Issue 資料

使用 GitLab MCP 取得完整資訊：
1. `mcp__GitLab_communication_server__get_issue` - 取得 Issue 詳細內容
2. `mcp__GitLab_communication_server__list_issue_discussions` - 取得所有討論串

顯示：
```
📋 Issue #123: 效能改進 - 不使用 pagesize=0
   Labels: performance, backend
   討論數: 5
```

### 3. AI 語義分析與拆分

AI 分析 Issue 的標題、描述和討論串，識別：
- **獨立的子任務**：每個子任務應是可獨立完成的工作單元
- **依賴順序**：判斷子任務間是否有先後順序
- **技術邊界**：識別跨模組/跨專案的邊界
- **複雜度估算**：為每個子任務建議 weight（1-5）

拆分原則：
- 每個子 Issue 應有明確的完成定義
- 避免過度拆分（尊重 maxSubIssues 上限）
- 繼承原 Issue 的相關 labels（若 inheritLabels 為 true）
- 每個子 Issue 的標題應清楚表達該子任務的範圍

### 4. 預覽拆分方案

使用 AskUserQuestion 展示拆分方案：

問題：「以下是 Issue #123 的拆分方案，請確認」

顯示表格：
```
原始 Issue: #123 - 效能改進 - 不使用 pagesize=0

子 Issue 方案：
┌───┬──────────────────────────────────┬────────────┬────────┐
│ # │ 標題                             │ Labels     │ Weight │
├───┼──────────────────────────────────┼────────────┼────────┤
│ 1 │ 移除 API 層的 pagesize=0 預設值  │ backend    │ 3      │
│ 2 │ 前端分頁元件改用 lazy loading    │ frontend   │ 5      │
│ 3 │ 資料庫查詢加入 LIMIT 限制        │ backend,db │ 2      │
│ 4 │ 撰寫效能測試基準                 │ testing    │ 2      │
└───┴──────────────────────────────────┴────────────┴────────┘

關聯類型: relates_to
```

選項：
  1. 確認，建立所有子 Issue
  2. 修改方案（增刪子 Issue 或調整內容）
  3. 只建立部分子 Issue
  4. 取消

### 5. 批量建立子 Issue

對每個確認的子 Issue：
1. `mcp__GitLab_communication_server__create_issue` 建立子 Issue
   - title: 子任務標題
   - description: AI 生成的詳細描述（包含完成定義）
   - labels: 繼承的 labels + 子任務特定 labels + defaults.labels
   - weight: AI 建議的複雜度
2. `mcp__GitLab_communication_server__create_issue_link` 建立與原 Issue 的關聯
   - link_type: 來自 config 的 split.linkType

### 6. 更新原始 Issue（可選）

使用 AskUserQuestion：
- 問題：「是否要在原始 Issue #123 中加入子 Issue 清單？」
- 選項：
  1. 是，在描述末尾加入清單（推薦）
  2. 是，新增一則備註
  3. 不需要

若選擇更新：
- 使用 `mcp__GitLab_communication_server__update_issue` 或 `create_issue_note`
- 加入格式：
  ```
  ## 子 Issue 清單

  - [ ] #124 - 移除 API 層的 pagesize=0 預設值
  - [ ] #125 - 前端分頁元件改用 lazy loading
  - [ ] #126 - 資料庫查詢加入 LIMIT 限制
  - [ ] #127 - 撰寫效能測試基準
  ```

### 7. 完成訊息

```
✓ Issue #123 已拆分為 4 個子 Issue：

  #124 - 移除 API 層的 pagesize=0 預設值
    URL: https://gitlab.example.com/BizForm/Home/-/issues/124

  #125 - 前端分頁元件改用 lazy loading
    URL: https://gitlab.example.com/BizForm/Home/-/issues/125

  #126 - 資料庫查詢加入 LIMIT 限制
    URL: https://gitlab.example.com/BizForm/Home/-/issues/126

  #127 - 撰寫效能測試基準
    URL: https://gitlab.example.com/BizForm/Home/-/issues/127

  所有子 Issue 已與原始 Issue 建立 relates_to 關聯
```

## GitLab MCP 工具使用

| 步驟 | 工具 | 用途 |
|------|------|------|
| 讀取 Issue | `get_issue` | 取得 Issue 詳細內容 |
| 讀取討論 | `list_issue_discussions` | 取得討論串 |
| 建立子 Issue | `create_issue` | 批量建立子 Issue |
| 建立關聯 | `create_issue_link` | 建立 relates_to 關聯 |
| 更新原 Issue | `update_issue` / `create_issue_note` | 加入子 Issue 清單 |

## 注意事項

- 子 Issue 數量不超過 config 中的 `split.maxSubIssues`
- 所有建立操作需使用者確認後才執行
- 不會關閉或刪除原始 Issue
- 若拆分過程中 API 呼叫失敗，會顯示已成功建立的子 Issue 清單
- 支援從 Issue 描述或討論串中識別子任務

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/weiting-tw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

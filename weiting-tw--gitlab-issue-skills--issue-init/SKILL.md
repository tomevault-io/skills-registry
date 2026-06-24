---
name: issue-init
description: 互動式初始化 .issue-config.json，自動偵測 GitLab 專案資訊並設定 Issue 管理規則。當使用者說「init issue」、「初始化 issue 設定」、「setup issue config」、「issue init」時觸發。 Use when this capability is needed.
metadata:
  author: weiting-tw
---

# Issue Init

互動式建立 `.issue-config.json`，類似 `npm init` 的體驗。自動偵測 GitLab 專案資訊，引導使用者完成設定。

## 使用方式

```
/issue-init
```

無需參數，全程互動引導。

## 工作流程

### Step 0: 環境檢查

1. 檢查 `.issue-config.json` 是否已存在（按優先順序搜尋）
   - 專案目錄: `./.issue-config.json`
   - 使用者目錄: `~/.claude/.issue-config.json`
   - 若任一存在 → 使用 AskUserQuestion 詢問：
     - 覆寫（重新建立）
     - 修改（互動式修改現有設定）
     - 取消
2. 驗證 GitLab MCP Server 可用性
   - 嘗試呼叫 `mcp__GitLab_communication_server__list_namespaces` 測試連線
   - 若失敗 → 顯示以下錯誤訊息並終止：
     ```
     ✗ GitLab MCP Server 無法連線

     請確認：
     1. ~/.claude/.mcp.json 中已設定 GitLab MCP Server
     2. GitLab Personal Access Token 有效且具備 api scope
     3. 網路連線正常
     ```

### Step 1: GitLab 連線偵測

按以下優先順序自動偵測：

1. **讀取 `.release-config.json`**（若存在）
   - 直接複用 `gitlab.projectId` 和 `gitlab.projectPath`
   - 顯示：「已從 .release-config.json 偵測到 GitLab 設定」

2. **解析 git remote URL**
   - 執行 `git remote get-url origin`
   - 解析出 group/project 路徑

3. **GitLab API 查詢**
   - 呼叫 `mcp__GitLab_communication_server__list_namespaces` 取得 Group 清單
   - 呼叫 `mcp__GitLab_communication_server__list_group_projects` 取得專案清單

使用 AskUserQuestion 確認偵測結果：
- 問題：「已偵測到以下 GitLab 設定，請確認」
- 選項：
  1. 確認，使用偵測到的設定（推薦）
  2. 選擇其他 Group/專案
  3. 手動輸入 Project ID 和 Path

### Step 2: 子專案設定（可選）

使用 `mcp__GitLab_communication_server__list_group_projects` 查詢 Group 下所有專案。

使用 AskUserQuestion：
- 問題：「是否要設定關聯子專案？（用於 /issue-create 的跨專案影響分析）」
- 選項：
  1. 自動加入偵測到的所有專案（推薦）
  2. 手動選擇要加入的專案
  3. 跳過，之後再設定

### Step 3: 預設 Labels

呼叫 `mcp__GitLab_communication_server__list_labels` 取得專案可用的 Labels。

使用 AskUserQuestion：
- 問題：「請選擇建立 Issue 時的預設標籤」
- 選項：
  1. 使用建議的預設標籤: ["from-claude"]（推薦）
  2. 從專案現有 Labels 中選擇
  3. 不使用預設標籤
  4. 自訂輸入

### Step 4: Issue 模板

使用 AskUserQuestion：
- 問題：「是否使用預設的 Issue 模板？（包含 bug、feature 兩種）」
- 選項：
  1. 使用預設模板（推薦）
  2. 自訂模板
  3. 跳過模板設定

預設模板內容：

**bug**（自動附加 label: Bug）:

描述模板包含以下區段：
- 狀況描述（客戶/客成訊息、工程人員簡述、反應用戶表格）
- 如何重現（重現步驟、出錯連結/截圖、重現環境裝置與瀏覽器 checklist）
- 完成定義（預期行為、API 邏輯、支援情境、可能解決方案）
- 關聯 Issue

**feature**（自動附加 label: Feature）:

描述模板包含以下區段：
- 功能描述（提出用戶表格）
- 完成定義
- 關聯 Issue

### Step 5: 進階設定

使用 AskUserQuestion：
- 問題：「是否要配置進階設定？（拆分規則、整理規則）」
- 選項：
  1. 使用預設值（推薦）— maxSubIssues: 8, staleDays: 90
  2. 自訂設定
  3. 跳過

若選擇自訂：
- 拆分規則：最大子 Issue 數（預設 8）、是否繼承標籤（預設 true）
- 整理規則：過期天數（預設 90）、是否自動標籤（預設 true）

### Step 6: 預覽與確認

顯示完整的 `.issue-config.json` 內容預覽（格式化 JSON）。

使用 AskUserQuestion：
- 問題：「以上是將要寫入的設定，確認嗎？」
- 選項：
  1. 確認，寫入檔案
  2. 返回修改
  3. 取消

### Step 7: 選擇存放位置與寫入

使用 AskUserQuestion 詢問存放位置：
- 問題：「設定檔要存放在哪裡？」
- 選項：
  1. 使用者目錄 ~/.claude/.issue-config.json（推薦，所有專案共用）
  2. 專案目錄 ./.issue-config.json（僅此專案使用）
  3. 兩者都寫入（專案層級可覆蓋使用者層級）

根據使用者選擇，執行對應的寫入操作：

1. 寫入 `.issue-config.json` 到選定位置
2. 合併更新 `.claude/settings.local.json`：
   - 讀取現有內容
   - 在 `permissions.allow` 中加入 Issue 相關的 MCP 權限（去重）
   - 需要加入的權限：
     ```
     mcp__GitLab_communication_server__create_issue
     mcp__GitLab_communication_server__update_issue
     mcp__GitLab_communication_server__get_issue
     mcp__GitLab_communication_server__list_labels
     mcp__GitLab_communication_server__create_label
     mcp__GitLab_communication_server__create_issue_link
     mcp__GitLab_communication_server__list_issue_links
     mcp__GitLab_communication_server__create_issue_note
     mcp__GitLab_communication_server__list_issue_discussions
     mcp__GitLab_communication_server__list_namespaces
     mcp__GitLab_communication_server__get_namespace
     mcp__GitLab_communication_server__list_issues
     mcp__GitLab_communication_server__list_milestones
     mcp__GitLab_communication_server__update_issue_note
     mcp__GitLab_communication_server__get_project
     mcp__GitLab_communication_server__list_projects
     mcp__GitLab_communication_server__execute_graphql
     mcp__GitLab_communication_server__delete_issue
     ```
   - 保留所有既有權限

### Step 8: 完成訊息

根據實際存放位置顯示對應訊息：

**若寫入使用者目錄：**
```
✓ ~/.claude/.issue-config.json 已建立（全域共用）
✓ .claude/settings.local.json 已更新（Issue MCP 權限已加入）

下一步：
  /issue-create "Issue 標題"       建立 Issue
  /issue-split #123                拆分 Issue
  /issue-discuss                   從對話建立 Issue
  /issue-organize                  整理舊 Issue
```

**若寫入專案目錄：**
```
✓ ./.issue-config.json 已建立（僅此專案）
✓ .claude/settings.local.json 已更新（Issue MCP 權限已加入）

下一步：
  /issue-create "Issue 標題"       建立 Issue
  /issue-split #123                拆分 Issue
  /issue-discuss                   從對話建立 Issue
  /issue-organize                  整理舊 Issue
```

**若寫入兩處：**
```
✓ ~/.claude/.issue-config.json 已建立（全域共用）
✓ ./.issue-config.json 已建立（專案覆蓋）
✓ .claude/settings.local.json 已更新（Issue MCP 權限已加入）

下一步：
  /issue-create "Issue 標題"       建立 Issue
  /issue-split #123                拆分 Issue
  /issue-discuss                   從對話建立 Issue
  /issue-organize                  整理舊 Issue
```

## .issue-config.json 完整結構

```json
{
  "$schema": "issue-config-v1",
  "configVersion": "1.0",
  "gitlab": {
    "groupPath": "BizForm",
    "defaultProjectId": "2183",
    "defaultProjectPath": "BizForm/Home",
    "projects": {
      "home": { "projectId": "2183", "projectPath": "BizForm/Home" }
    }
  },
  "defaults": {
    "labels": ["from-claude"],
    "confidential": false,
    "dryRun": true,
    "batchLimit": 50
  },
  "templates": {
    "bug": {
      "labels": ["Bug"],
      "titlePrefix": "[Bug]",
      "descriptionTemplate": "# 狀況描述\n\n- 客戶/客成訊息 (或附上 Mattermost 討論連結)\n    \n- 工程人員簡述\n    \n\n| 反應的用戶 | 聯繫人員 |\n| ---------- | ---------- |\n| xxx        | xxxx       |\n\n# 如何重現\n\n## 重現步驟\n  1. \n  2. \n\n- 出錯表單/畫面的連結/截圖\n    - 表單、表單樣板網址請記得加上參數指定站台 `?depotId=gss_test`\n\n- 重現環境\n  - 裝置\n    - [ ] Win\n    - [ ] Mac\n    - [ ] iPhone\n    - [ ] iPad\n    - [ ] Android\n  - 特定瀏覽器\n    - [ ] Chrome\n    - [ ] Edge\n    - [ ] Safari\n    - [ ] FireFox\n\n# 完成定義\n\n## 完整的預期行為\n\n- 設計稿/畫面截圖標示\n  \n  \n- API 預期回傳資料、處理邏輯\n  - [API 名稱](swagger 連結)\n  - \n\n- 需支援的情境/範例情境\n  - \n\n## 可能解決方案\n\n- \n\n\n\n# 關聯 Issue\n\n- \n"
    },
    "feature": {
      "labels": ["Feature"],
      "titlePrefix": "[Feature]",
      "descriptionTemplate": "# 功能描述\n\n\n| 提出的用戶 | 聯繫的人員 |\n| ---------- | ---------- |\n| xxx        | xxxx       |\n\n# 完成定義\n\n\n\n# 關聯 Issue\n\n\n"
    }
  },
  "split": {
    "maxSubIssues": 8,
    "inheritLabels": true,
    "linkType": "relates_to"
  },
  "organize": {
    "staleDays": 90,
    "autoLabels": true
  }
}
```

## GitLab MCP 工具使用

| 步驟 | 工具 | 用途 |
|------|------|------|
| 環境檢查 | `list_namespaces` | 測試 MCP 連線 |
| 連線偵測 | `list_namespaces` | 取得 Group 清單 |
| 連線偵測 | `list_group_projects` | 取得專案清單 |
| 子專案 | `list_group_projects` | 列出 Group 下所有專案 |
| Labels | `list_labels` | 取得可用標籤 |

## 注意事項

- Token 不存在 config 中，由 GitLab MCP Server 管理（~/.claude/.mcp.json）
- 整份 .issue-config.json 全部可安全 commit 到 repo
- 多次執行 /issue-init 不會產生重複的權限條目（冪等合併）
- 若偵測到 .release-config.json，優先複用其 GitLab 設定

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/weiting-tw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

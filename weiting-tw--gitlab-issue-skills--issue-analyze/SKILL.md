---
name: issue-analyze
description: 分析 GitLab Issue 的影響範圍，跨 repo 搜尋相關程式碼與 MR，產生影響評估報告。當使用者說「分析 issue」、「analyze issue」、「issue 影響分析」、「這個 issue 會影響什麼」、「評估 issue」時觸發。 Use when this capability is needed.
metadata:
  author: weiting-tw
---

# Issue Analyze

分析 GitLab Issue 的影響範圍。跨 30+ 子專案搜尋相關程式碼與 MR，產生結構化影響評估報告。Phase 1 為「觀察者模式」，只產生報告不建 MR。

## 使用方式

```bash
/issue-analyze #123
/issue-analyze --project=backend #45
/issue-analyze https://gitlab.example.com/BizForm/Home/-/issues/123
/issue-analyze #123 --scope=bizform-web,backend,elsa-server
/issue-analyze #123 --depth=shallow
/issue-analyze #123 --write-back
```

### 參數說明

| 參數 | 必要 | 說明 |
|------|------|------|
| `#IID` 或 URL | 是 | 目標 Issue |
| `--project` | 否 | 專案 key（預設 defaultProjectPath） |
| `--scope` | 否 | 限定搜尋的子專案（逗號分隔 key），預設使用智慧選取 |
| `--depth` | 否 | `shallow`（僅搜尋 repo tree）/ `deep`（含檔案內容，預設） |
| `--write-back` | 否 | 將報告寫回 Issue 描述底部 |

## 前置條件

需要 `.issue-config.json` 存在。按以下優先順序搜尋：
1. 專案目錄: `./.issue-config.json`
2. 使用者目錄: `~/.claude/.issue-config.json`

若均不存在，提示：「找不到 .issue-config.json，請先執行 /issue-init 建立設定。」

## 工作流程

### Step 1: 前置檢查與設定讀取

從搜尋到的 `.issue-config.json` 讀取（專案層級優先於使用者層級）：
- `gitlab.projects` — 所有可搜尋的子專案清單
- `gitlab.defaultProjectPath` / `defaultProjectId`
- `analyze` 設定區塊（搜尋深度、安全策略、專案領域映射等）

檢查邏輯：
- `.issue-config.json` 不存在 → 提示執行 `/issue-init`
- 解析 `--project` 參數 → 映射到 `projects[key]` 取得 projectId / projectPath
- 若有 `analyze.securityPolicy`，初始化安全過濾器

### Step 2: 取得 Issue 完整資訊

**三個呼叫可並行：**
1. `mcp__GitLab_communication_server__get_issue` — 取得 Issue 詳細內容
2. `mcp__GitLab_communication_server__list_issue_discussions` — 取得所有討論串
3. `mcp__GitLab_communication_server__list_issue_links` — 取得關聯 Issue

**AI 語義萃取：**
從 title、description、discussions、labels 中萃取：
1. 技術關鍵字 — API 名稱、元件名稱、資料庫表名、函式名
2. 模組關鍵字 — 前端/後端/workflow/表單/通知等模組標示
3. 檔案線索 — 描述中直接提及的檔案路徑或類名
4. 領域關鍵字 — 業務術語（如「計算欄位」「子表單」「簽核」）

顯示進度：
```
📋 Issue #123: [Bug] 計算欄位在子表單中顯示 NaN
   Labels: bug, frontend
   討論數: 3
   萃取關鍵字: 計算欄位, SubForm, NaN, CalculationEngine
```

### Step 3: 智慧範圍選取 — 三層漏斗策略

#### Layer 1: 領域分類器（0 API 呼叫，純推理）

使用 config 中 `analyze.projectDomains` 的靜態映射表，將關鍵字比對到專案領域標籤。

映射邏輯範例：
| 關鍵字類型 | 關鍵字範例 | 映射到專案 |
|-----------|-----------|-----------|
| 前端 UI | 表單元件、CSS、Vue、React | bizform-web, formeditor |
| 後端 API | Controller、API、Swagger | backend |
| Workflow | 簽核、Elsa、流程 | elsa-server, elsa-client |

若使用者用 `--scope` 明確指定，跳過 Layer 1。

→ 選出 5-8 個候選專案

#### Layer 2: Repo Tree 快速掃描（5-8 次 API 呼叫，可並行）

對候選專案使用 `mcp__GitLab_communication_server__get_repository_tree`（`recursive` 必須為 boolean 值 true）。

AI 根據檔案結構判斷：
- 是否有相關的目錄/檔案路徑
- 過濾掉不相關的專案

**安全過濾：** 所有路徑必須通過安全檢查：
1. 路徑是否匹配 `securityPolicy.blockedPaths` → 跳過
2. 副檔名是否在 `securityPolicy.allowedExtensions` → 否則跳過
3. 專案是否在 `securityPolicy.blockedProjects` → 跳過整個專案

→ 確認 3-5 個相關專案 + 各專案可能相關的檔案路徑

#### Layer 3: 檔案內容深度搜尋（僅 depth=deep 時）

使用 `mcp__GitLab_communication_server__get_file_contents` 讀取相關檔案。

限制：
- 每個專案最多讀取 `analyze.maxFilesPerRepo`（預設 10）個檔案
- 優先讀取控制器、服務層、元件入口等關鍵檔案
- 跳過測試檔案（除非 Issue 明確涉及測試）

### Step 4: 搜尋相關 MR

對每個相關專案（可並行）：
1. `mcp__GitLab_communication_server__list_merge_requests` — search 已合併 MR（state="merged"）
2. `mcp__GitLab_communication_server__list_merge_requests` — search 開放中 MR（state="opened"）

搜尋關鍵字策略：
- Issue title 核心詞（去除 [Bug]/[Feature] prefix）
- Issue IID（搜尋 MR 描述中提到 #123 的）
- 技術關鍵字擴展（若結果不足）

每專案最多取 `analyze.maxMRsPerRepo`（預設 10）個 MR。

對最相關的 MR（最多 `analyze.maxDiffMRs`，預設 5 個），取得 diff：
- `mcp__GitLab_communication_server__get_merge_request_diffs`

從 diff 萃取：修改檔案、新增/刪除行數、修改的函式/類名。

### Step 5: 影響分析與信心度計算

#### 5a. 影響檔案相關性分數

| 分數來源 | 權重 |
|----------|------|
| Issue 描述中直接提及的檔案 | +40 |
| MR diff 中修改過的檔案 | +30 |
| Repo Tree 中目錄名稱匹配 | +15 |
| 檔案內容中包含關鍵字 | +10 |
| 關聯 Issue 提及的檔案 | +5 |

#### 5b. 信心度分級系統

| 等級 | 分數 | 含義 |
|------|------|------|
| HIGH | 80-100 | 影響範圍明確，建議可直接採用 |
| MEDIUM | 50-79 | 影響範圍大致正確，建議人工複查 |
| LOW | 20-49 | 資訊不足，僅供參考 |
| INSUFFICIENT | 0-19 | 無法有效分析，建議補充 Issue 描述 |

信心度因子加權：
- Issue 描述品質 (30%)
- 程式碼匹配度 (25%)
- MR 佐證 (25%)
- 跨專案一致性 (10%)
- 討論串資訊 (10%)

#### 5c. 複雜度預估

- `S` — 1-2 檔案，單專案
- `M` — 3-5 檔案或跨 2 專案
- `L` — 6-10 檔案或跨 3+ 專案
- `XL` — 10+ 檔案或核心模組變更

### Step 6: 產生分析報告

報告以 Markdown 格式輸出到終端機，包含：
1. 摘要表格（Issue、類型、信心度、複雜度）
2. 影響檔案清單（按專案分組，含相關性分數與說明）
3. 相關 MR 清單（MR ID、專案、狀態、相關性）
4. 建議修改方向（主要修改點、連帶修改、驗證點）
5. 風險與注意事項
6. 安全過濾記錄（被排除的檔案/專案）
7. 分析方法說明（搜尋的專案、關鍵字、API 呼叫統計）

#### 完整報告格式範例

```markdown
---

## 🔍 Issue 影響分析報告

> 由 Claude Code `/issue-analyze` 自動產生 | {timestamp}
> [信心度: {LEVEL} ({score}/100)] | [複雜度: {S/M/L/XL}]

### 摘要

| 項目 | 值 |
|------|-----|
| Issue | #{iid} - {title} |
| 類型 | {Bug/Feature} |
| 影響專案 | {project1}, {project2} |
| 影響檔案 | {N} 個 |
| 相關 MR | {N} 個 ({merged} merged, {opened} open) |
| 預估複雜度 | {S/M/L/XL} |

### 影響檔案

#### {project-name} ({N} 檔案)

| 檔案 | 相關性 | 說明 |
|------|--------|------|
| `{path}` | {score}/100 | {description} |

### 相關 MR

| MR | 專案 | 狀態 | 相關性 |
|----|------|------|--------|
| !{iid} - {title} | {project} | {merged/open} | {description} |

### 建議修改方向

1. **主要修改點**: {description}
2. **連帶修改**: {description}
3. **驗證點**: {description}

### 風險與注意事項

- {risk items}

### 安全過濾記錄

{filtered items or "無被過濾的項目"}

### 分析方法

- 搜尋專案: {list}
- 搜尋關鍵字: {keywords}
- Layer 1 排除: {N} 個不相關專案
- Repo Tree 掃描: {N} 個專案
- 檔案內容讀取: {N} 個檔案
- MR 搜尋: {N} 個專案, 找到 {N} 個相關 MR
```

#### 精簡版（用於 write-back 的摘要選項）

```markdown
---

## 🔍 Issue 影響分析摘要

> [信心度: {LEVEL}] | [複雜度: {S/M/L/XL}] | {date} by Claude Code

**影響檔案 ({N})**:
- `{project}`: {file1}, {file2}

**相關 MR**: !{iid} ({state}), ...

**建議方向**: {summary}

---
```

### Step 7: (可選) 寫回 Issue

分析報告完成後，使用 AskUserQuestion 詢問：
- 問題：「是否將分析報告寫回 Issue #{iid} 的討論串？」
- 選項：
  1. 確認，寫回完整報告（含折疊區塊）
  2. 只寫摘要（精簡版）
  3. 取消

寫回方式 — 使用 GraphQL `createNote` mutation：

```graphql
mutation CreateNote($noteableId: NoteableID!, $body: String!) {
  createNote(input: {
    noteableId: $noteableId
    body: $body
  }) {
    note { id body url createdAt }
    errors
  }
}
```

變數：
- `noteableId`: `gid://gitlab/Issue/{issue_id}`（注意：使用 `get_issue` 回傳的 `id` 欄位，非 `iid`）
- `body`: 報告內容，使用 `<details>` HTML 標籤包裝為可折疊區塊

報告格式（折疊版）：
```markdown
<details>
<summary>

## 🔍 Issue 影響分析報告
> 信心度: **{LEVEL} ({score}/100)** | 複雜度: **{S/M/L/XL}** | {date} by Claude Code `/issue-analyze`

</summary>

{完整報告內容...}

</details>

---
*由 Claude Code `/issue-analyze` 自動產生 — [Phase 1 觀察者模式]*
```

**注意：**
- 不使用 `create_issue_note`（有 404 "Discussion Not Found" bug）
- 不使用 `update_issue` 修改描述（會污染原始描述）
- GraphQL `createNote` 建立獨立 comment，不影響原始 Issue 內容

### Step 8: 完成訊息

```
✓ Issue #{iid} 影響分析完成

  信心度: {LEVEL} ({score}/100)
  複雜度: {S/M/L/XL} ({N} 檔案, {N} 專案)

  影響範圍:
  - {project}: {N} 檔案 ({file list})

  相關 MR: {N} 個 ({merged} merged, {open} open)

  建議修改方向: {summary}

  報告已寫回 Issue: {是/否}
```

## GitLab MCP 工具使用

| Step | 工具 | 用途 | 可並行 |
|------|------|------|--------|
| 2 | `get_issue` | 取得 Issue 詳細內容 | 是 (2a-2c) |
| 2 | `list_issue_discussions` | 取得討論串 | 是 |
| 2 | `list_issue_links` | 取得關聯 Issue | 是 |
| 3 | `get_repository_tree` | 掃描 repo 檔案結構 | 是 (5-8 並行) |
| 3 | `get_file_contents` | 讀取檔案內容 | 批次 (最多 30) |
| 4 | `list_merge_requests` | 搜尋相關 MR | 是 (每專案並行) |
| 4 | `get_merge_request_diffs` | 取得 MR diff | 是 (最多 5 並行) |
| 7 | `get_issue` | 取得 Issue 全域 ID（寫回用） | - |
| 7 | `execute_graphql` | GraphQL createNote 建立 comment | - |

## 安全機制

### Phase 1 觀察者模式硬性限制

| 允許 | 禁止 |
|------|------|
| 讀取 Issue / 討論 / 關聯 | 建立/刪除 Issue |
| 讀取 repo 檔案結構和內容 | 任何 git push / MR 建立 |
| 搜尋 MR 和 diff | 任何 MR 操作（approve, merge） |
| 寫回分析報告到 Issue 討論串（comment） | 修改 Issue 的 labels/assignee/state |

### 路徑安全過濾

- `blockedPaths`: 禁止讀取的路徑 glob（.env、secrets、CI/CD 等）
- `blockedProjects`: 禁止搜尋的專案（infra、deploy 等）
- `allowedExtensions`: 只允許的副檔名白名單（.cs、.ts、.tsx、.js 等）

## 效能預估

| 模式 | API 呼叫數 | 預估耗時 |
|------|-----------|---------|
| shallow | 15-25 | 10-15 秒 |
| deep（預設） | 25-58 | 20-40 秒 |

## 注意事項

- `get_repository_tree` 的 `recursive` 參數必須傳 boolean true，不能傳字串 "true"
- `create_issue_note` 有 404 bug（"Discussion Not Found"），寫回改用 GraphQL `createNote` mutation
- 寫回 Issue 時，使用 `<details>` 折疊區塊包裝報告，避免佔據版面
- GraphQL `noteableId` 使用 `gid://gitlab/Issue/{id}` 格式，`id` 來自 `get_issue` 回傳的 `id` 欄位（非 `iid`）
- 大量 API 呼叫時注意 GitLab rate limit（300 req/min）
- 報告末尾加入來源標註「由 Claude Code /issue-analyze 自動產生」

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/weiting-tw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

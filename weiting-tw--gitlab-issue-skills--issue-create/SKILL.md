---
name: issue-create
description: 智慧建立 GitLab Issue，支援討論式建立和指令式建立。當使用者討論 bug/功能需求到一個段落時，主動詢問是否建立 Issue。也可用指令直接建立。當使用者說「建立 issue」、「create issue」、「新增 issue」、「開 issue」、「開票」，或在討論 bug/feature 時說「好」、「開吧」、「建起來」時觸發。 Use when this capability is needed.
metadata:
  author: weiting-tw
---

# Issue Create

智慧建立 GitLab Issue，支援兩種使用模式：**討論模式**（自然對話後建立）和**指令模式**（直接建立）。

## 使用模式

### 模式一：討論模式（推薦）

在自然對話中討論問題或需求，AI 會在適當時機主動詢問是否建立 Issue。

**觸發時機**：當對話中出現以下情境時，AI 應主動提出建立 Issue：
- 討論了一個具體的 bug（重現步驟、錯誤現象）
- 討論了一個新功能需求（功能描述、預期行為）
- 使用者描述了客戶/客成回報的問題
- 討論到了具體的解決方案或行動項目

**對話範例**：
```
使用者：表單送出後，如果有計算欄位引用了子表單的值，結果會顯示 NaN。
        客戶 A 公司的 John 有回報這個問題。

AI：    這看起來是計算欄位在子表單值更新時沒有正確取值的問題。
        可能是子表單的資料在送出時尚未同步到計算欄位的上下文中。

        要不要建立一個 Bug Issue 來追蹤這個問題？
        我可以根據我們剛才的討論自動填寫模板。

使用者：好

AI：    [進入 Issue 預覽與確認流程，自動從對話中萃取資訊填入模板]
```

**AI 主動提問的格式**：
使用 AskUserQuestion：
- 問題：「要不要建立一個 [Bug/Feature] Issue 來追蹤這個問題？」
- 選項：
  1. 好，建立 Issue（AI 自動從對話萃取內容填入模板）
  2. 再討論一下，先不建
  3. 不用

若使用者同意，AI 從對話上下文中萃取資訊，自動填入模板的各區段，然後進入 **Step 5（預覽與確認）** 流程。

### 模式二：指令模式

直接使用指令建立 Issue。

```
/issue-create 修正登入頁面的驗證問題
```

```
/issue-create --template=bug 登入驗證失敗
```

```
/issue-create --project=api --template=feature --milestone="Sprint 62" 新增深色模式
```

參數說明：
- `描述`（必要）：Issue 的自然語言描述
- `--template`（可選）：使用指定模板（bug / feature），若未指定則 AI 自動判斷
- `--project`（可選）：指定目標專案的 key（對應 config 中 gitlab.projects），預設為 defaultProjectPath
- `--labels`（可選）：額外標籤，逗號分隔
- `--milestone`（可選）：指定里程碑名稱

## 前置條件

需要 `.issue-config.json` 存在。按以下優先順序搜尋：
1. 專案目錄: `./.issue-config.json`
2. 使用者目錄: `~/.claude/.issue-config.json`

若均不存在，提示：
```
✗ 尚未初始化 Issue 設定

找不到 .issue-config.json，請先執行 /issue-init 建立設定。
```

## 工作流程

### 1. 讀取設定

從搜尋到的 `.issue-config.json` 讀取（專案層級優先於使用者層級）：
- `gitlab.defaultProjectPath`（或 `--project` 覆寫）
- `defaults.labels`
- `templates`（若指定 `--template` 或 AI 自動判斷）

### 2. 內容來源判斷

根據觸發模式決定內容來源：

**討論模式**：
- 從當前對話上下文中萃取資訊
- 自動識別：問題描述、客戶資訊、重現步驟、技術分析、解決方案
- 將萃取的資訊對應到模板區段

**指令模式**：
- 從使用者提供的描述文字中提取資訊

### 3. 智慧分析

AI 根據內容來源進行分析：
- **自動判斷 Issue 類型**：根據關鍵字判斷是 bug / feature
  - bug 關鍵字：修正、修復、fix、bug、錯誤、問題、異常、crash、壞掉、不正常、失敗
  - feature 關鍵字：新增、新功能、feature、實作、implement、需求、開發、建立
- **提取關鍵資訊**：填入對應模板的各區段
  - Bug 模板：填入「狀況描述」、「如何重現」、「完成定義」等區段
  - Feature 模板：填入「功能描述」、「完成定義」等區段
- **建議標籤組合**：從 config 中的 templates.labels + defaults.labels 組合
- **建議優先級**：根據描述中的緊急程度

### 4. 跨專案影響分析（可選）

若 config 中有設定 `gitlab.projects`（多專案）：
1. 使用 `mcp__GitLab_communication_server__list_issues` 搜尋各子專案的相關 Issue
2. 分析此需求可能影響的專案範圍
3. 在 Issue 描述中加入「影響範圍」區段

### 5. 重複檢查

使用 `mcp__GitLab_communication_server__list_issues` 搜尋相似 Issue：
- 以 title 關鍵字搜尋
- 若找到相似 Issue → 使用 AskUserQuestion 顯示：
  - 問題：「找到以下相似的 Issue，是否仍要建立新 Issue？」
  - 列出相似 Issue（標題 + URL）
  - 選項：
    1. 仍要建立新 Issue
    2. 在既有 Issue 上新增備註
    3. 取消

### 6. 預覽與確認

使用 AskUserQuestion 展示完整的 Issue 預覽：
- 問題：「即將建立以下 Issue，確認嗎？」
- 顯示：
  ```
  專案: BizForm/Home
  標題: 登入驗證失敗導致無法登入
  Labels: Bug, from-claude
  描述:
    # 狀況描述

    - 客戶/客成訊息 (或附上 Mattermost 討論連結)
        [相關討論連結]

    - 工程人員簡述
        使用者輸入正確帳密後，系統仍顯示驗證失敗

    | 反應的用戶 | 聯繫人員 |
    | ---------- | ---------- |
    | XXX 公司   | John       |

    # 如何重現

    ## 重現步驟
      1. 開啟登入頁面
      2. 輸入正確的帳號密碼
      3. 點擊登入按鈕

    - 出錯表單/畫面的連結/截圖
        - https://example.com/login?depotId=gss_test

    - 重現環境
      - 裝置
        - [x] Win
        - [ ] Mac
        ...
  ```
- 選項：
  1. 確認建立
  2. 修改內容（提供修改指示）
  3. 取消

### 6.5 Label 驗證

在建立 Issue 前，檢查所有要附加的 Labels 是否存在：
1. 使用 `mcp__GitLab_communication_server__list_labels` 查詢專案現有 Labels
2. 比對預計附加的 Labels
3. 若有不存在的 Label → 使用 AskUserQuestion 詢問：
   - 問題：「以下 Labels 在專案中不存在：[label1, label2]」
   - 選項：
     1. 建立新 Label 並繼續
     2. 移除不存在的 Label 繼續建立
     3. 取消
4. 若選擇建立 → 使用 `mcp__GitLab_communication_server__create_label` 建立

### 7. 建立 Issue

使用 `mcp__GitLab_communication_server__create_issue` 建立 Issue：
- project_id: 來自 config
- title: 組合後的標題
- description: 填充後的模板內容
- labels: 組合後的標籤（逗號分隔字串）
- milestone_id: 若指定，先用 list_milestones 查詢對應 ID

### 8. 建立跨專案關聯（可選）

若影響分析發現相關 Issue：
- 使用 AskUserQuestion 詢問是否建立關聯
- 使用 `mcp__GitLab_communication_server__create_issue_link` 建立 `relates_to` 關聯

### 9. 完成訊息

```
✓ Issue 已建立: #456 - [Bug] 登入驗證失敗
  URL: https://gitlab.example.com/BizForm/Home/-/issues/456
  Labels: bug, priority::medium, from-claude

  關聯 Issue:
  - relates_to #234 (BizForm/API)
```

## GitLab MCP 工具使用

| 步驟 | 工具 | 用途 |
|------|------|------|
| 重複檢查 | `list_issues` | 搜尋相似 Issue |
| 跨專案分析 | `list_issues` | 搜尋各專案相關 Issue |
| 里程碑查詢 | `list_milestones` | 取得 milestone ID |
| Label 驗證 | `list_labels` | 查詢專案現有 Labels |
| 建立 Label | `create_label` | 建立新 Label |
| 建立 Issue | `create_issue` | 建立 Issue |
| 建立關聯 | `create_issue_link` | 建立 Issue 間關聯 |
| 新增備註 | `create_issue_note` | 在既有 Issue 上新增備註 |

## 模板定義

### Bug 模板

使用時自動附加 label: `Bug`

**注意：** GitLab quick action `/label` 由 API 的 labels 參數自動處理，不需要放在描述中。

~~~markdown
# 狀況描述

- 客戶/客成訊息 (或附上 Mattermost 討論連結)

- 工程人員簡述


| 反應的用戶 | 聯繫人員 |
| ---------- | ---------- |
| xxx        | xxxx       |

# 如何重現

## 重現步驟
  1.
  2.

- 出錯表單/畫面的連結/截圖
    - 表單、表單樣板網址請記得加上參數指定站台 `?depotId=gss_test`

- 重現環境
  - 裝置
    - [ ] Win
    - [ ] Mac
    - [ ] iPhone
    - [ ] iPad
    - [ ] Android
  - 特定瀏覽器
    - [ ] Chrome
    - [ ] Edge
    - [ ] Safari
    - [ ] FireFox

# 完成定義

## 完整的預期行為

- 設計稿/畫面截圖標示


- API 預期回傳資料、處理邏輯
  - [API 名稱](swagger 連結)
  -

- 需支援的情境/範例情境
  -

## 可能解決方案

-



# 關聯 Issue

-
~~~

### Feature 模板

使用時自動附加 label: `Feature`

**注意：** GitLab quick action `/label` 由 API 的 labels 參數自動處理，不需要放在描述中。

~~~markdown
# 功能描述


| 提出的用戶 | 聯繫的人員 |
| ---------- | ---------- |
| xxx        | xxxx       |

# 完成定義



# 關聯 Issue


~~~

## 注意事項

- 所有建立操作都需要使用者確認後才執行
- AI 自動判斷的模板類型可被 --template 參數覆寫
- defaults.labels 中的標籤會自動附加到所有新建 Issue
- 若 GitLab 中不存在指定的 label，會提示是否要建立新 label

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/weiting-tw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

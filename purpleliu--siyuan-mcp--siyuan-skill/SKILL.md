---
name: siyuan-skill
description: OpenClaw workflows for SiYuan Note via the siyuan MCP server (mcporter). Use when this capability is needed.
metadata:
  author: purpleliu
---

# 思源筆記（SiYuan）Skill（for OpenClaw）

本 Skill 的目標是把「思源筆記 MCP tools」組織成**穩定、可重複使用**的工作流（workflow）。

- **MCP** 負責提供工具（tools）
- **Skill** 負責告訴 OpenClaw：在什麼情境用哪些 tools、怎麼組合、怎麼把結果寫回筆記

> 原則：MCP 與 Skill 分離（最小耦合）。Skill 不描述 MCP 內部實作，只描述「怎麼用」。

---

## 0) 先決條件（Setup）

1) 準備 token/baseUrl：

```bash
export SIYUAN_TOKEN="your_token_here"
export SIYUAN_BASE_URL="http://127.0.0.1:6806"
```

2) mcporter 需設定一個名為 `siyuan` 的 MCP server：

```bash
mcporter config add siyuan \
  --command "siyuan-mcp stdio --token $SIYUAN_TOKEN --baseUrl $SIYUAN_BASE_URL"
```

3) 呼叫方式：

```bash
mcporter call siyuan.<tool_name> key=value
```

列出工具與參數：

```bash
mcporter list siyuan --all-parameters
```

---

## 1) 預設設定（本環境）

- **Daily Note 筆記本**：`Daily Note`
- **Daily Note notebook_id**：`20260205161632-m9mibni`
- **收集策略（Inbox）**：所有快速記錄一律先寫進 Daily Note（純 Markdown）
- **TODO 格式**：Markdown checkbox
  - 未完成：`- [ ] ...`
  - 已完成：`- [x] ...`

---

## 2) 核心工作流（MVP）

### Workflow A：快速記錄（寫進今日 Daily Note）

適用：你說「幫我記一下…」「快速記錄…」「備忘…」

工具鏈：`append_to_daily_note`

```bash
mcporter call siyuan.append_to_daily_note \
  notebook_id="20260205161632-m9mibni" \
  content="- 2026-02-05 16:30 這是一則快速記錄"
```

### Workflow B：新增待辦（checkbox，寫進今日 Daily Note）

適用：你說「提醒我…」「TODO…」「待辦…」

工具鏈：`append_to_daily_note`

```bash
mcporter call siyuan.append_to_daily_note \
  notebook_id="20260205161632-m9mibni" \
  content="- [ ] 明天買電池"
```

### Workflow C：列出近 N 天未完成待辦（Daily Note 範圍）

適用：你說「我有哪些還沒做完？」「列出未完成待辦」

工具鏈：`list_daily_note_todos`

```bash
mcporter call siyuan.list_daily_note_todos \
  notebook_id="20260205161632-m9mibni" \
  days=7
```

預期回傳（示意）：

```json
[
  {
    "text": "明天買電池",
    "done": false,
    "date": "2026-02-05",
    "document_id": "...",
    "line_no": 12
  }
]
```

### Workflow D：搜尋筆記（關鍵字/標籤/檔名）

適用：你說「幫我找…」「搜尋…」

工具鏈：`unified_search` →（必要時）`get_document_content`

```bash
# 內容搜尋
mcporter call siyuan.unified_search content="kubernetes" limit=10

# 檔名搜尋
mcporter call siyuan.unified_search filename="週報" limit=10

# 標籤搜尋（不含 #）
mcporter call siyuan.unified_search tag="project" limit=10
```

### Workflow E：整理今天（把 Daily Note 分區、列待辦、產出歸檔候選）

適用：你說「整理今天」「幫我整理今日筆記」

目標（MVP）：
- 不改變你原本寫法太多（**純 Markdown**）
- 把內容整理成固定區塊：Inbox / TODO / 今日整理（摘要、重點、可歸檔）
- 可歸檔項目**先列候選**，不自動搬移（符合最小原則）

建議工具鏈（可靠版本）：
1) 定位/建立今日 Daily Note：
- 讀取筆記本 dailyNoteSavePath（可選）
- 用預設路徑（或依 notebook 設定）找到今天的 hpath
- `get_ids_by_hpath` → 若不存在可用 `create_document` 建立

2) 讀取今日內容：
- `get_document_content(document_id)`

3) 列出未完成 TODO（近 1 天即可）：
- `list_daily_note_todos(notebook_id, days=1)`

4) 產出整理段落並寫回：
- `append_to_document(document_id, content)`

範例（找到今日文件後）：

```bash
mcporter call siyuan.list_daily_note_todos \
  notebook_id="20260205161632-m9mibni" \
  days=1

mcporter call siyuan.append_to_document \
  document_id="<today_document_id>" \
  content="\n\n## 今日整理（AI）\n\n- 摘要：...\n- 重點：...\n- 未完成 TODO：...\n- 可歸檔（候選）：...\n"
```

### Workflow F：產出週報（先產生 Markdown，再決定是否寫回）

適用：你說「幫我產出本週週報」

建議流程（最小版本）：
1. 先用 `list_daily_note_todos` 拉出本週未完成項目
2. 再用搜尋/最近更新/讀取內容補充「本週完成事項」素材
3. 由 Agent 產出一份純 Markdown（此階段可先不自動寫回，避免誤寫）

> 週報自動寫回（create_document）放在 M2 里程碑後再固定模板。

---

## 3) 寫回與整理（後續擴充方向，非 MVP）

當核心穩定後，再逐步加入：
- Inbox → 分類 → 歸檔（建立固定資料夾與規則）
- 去重（找相似筆記、合併段落、建立索引頁）
- 引導使用思源屬性/模板/標籤（讓 AI 自動補齊）

---

## 4) 筆記整理規範

### 連結格式

思源內部連結使用以下格式，點擊可直接跳轉：

```markdown
[顯示名稱](siyuan://blocks/文件ID)
```

範例：
```markdown
- [維運客戶](siyuan://blocks/20250108190236-49uy54d)
- [SOP文件](siyuan://blocks/20250320164549-8nwn7q7)
```

### 母文件標準模板

有子文件的母文件，本身應作為「目錄/摘要」頁。標準模板：

```markdown
## 摘要
- 主要內容/業務說明
- 負責人：（待補）

## 快速入口
- [最新/常用項目](siyuan://blocks/ID)

## 目錄
### 分類一
- [項目](siyuan://blocks/ID)

### 分類二
- [項目](siyuan://blocks/ID)
```

### 結構原則

| 原則 | 說明 |
|------|------|
| **混合式分類** | 客戶專屬 → 客戶資料夾；可共用 → 抽到 SOP/教育訓練 |
| **母文件 = 目錄/摘要** | 有子文件的母文件，本身要能輸入內容，作為入口頁 |
| **索引放母文件** | 索引內容直接放在母文件，不額外開子文件 |
| **單一索引頁** | 每個筆記本只需一個總索引，整合所有入口 |

### 內容格式

| 原則 | 說明 |
|------|------|
| **純 Markdown** | 不使用特殊格式 |
| **TODO 用 checkbox** | `- [ ]` 待辦 / `- [x]` 完成 |
| **不重複 H1** | 思源自動產生 H1（標題），內容從 `##` 開始 |
| **多行內容用 content_file** | 避免 `\n` 字面化問題 |

### 清理與歸檔

| 原則 | 說明 |
|------|------|
| **未命名/重複 → `_duplicates`** | 集中暫放，不直接刪除 |
| **散落文件 → 歸位** | 根層的文件移到對應分類下 |
| **整理前先快照** | 可回滾 |
| **歸檔需確認** | 不自動刪除，等使用者確認 |

### 索引頁設計規範

**設計原則：**
1. **引用塊開頭**：`> 💼 筆記本名稱` 作為視覺提示
2. **Emoji 標題**：例如 🚀 快速入口、📂 結構、📌 其他…
3. **表格化入口**：用表格呈現「分類／說明」，提高可讀性
4. **可點擊連結**：一律使用 `[名稱](siyuan://blocks/ID)`
5. **分隔線區分段落**：用 `---` 分隔不同區塊
6. **結構說明**：用 code block 呈現目錄樹，協助理解整體架構

**標準區塊順序（建議）：**
1. 引用塊提示（筆記本名稱 + emoji）
2. 🚀 快速入口（主要分類表格）
3. 🔥 近期活躍 / 常用項目（如適用）
4. 📋 速查表（如 SOP）
5. 📌 其他常用連結
6. 📂 結構說明（目錄樹）
7. 使用規則 / 分類原則

**極簡範例：**
```markdown
> 💼 筆記本名稱

## 🚀 快速入口

| 分類 | 說明 |
|------|------|
| [維運客戶](siyuan://blocks/ID) | 客戶專屬資料 |
| [SOP文件](siyuan://blocks/ID) | 標準作業流程 |

---

## 📂 結構

```
筆記本/
├─ 索引
├─ 分類A/
└─ 分類B/
```
```

---

## 5) 常見注意事項

### ⚠️ 多行內容傳遞（重要！）

當你用 `mcporter call` 傳遞多行 Markdown 內容時，**不要**直接在參數裡寫 `\n`，會變成字面文字。

**❌ 錯誤寫法**（會變成 `\n` 字面文字）：
```bash
mcporter call siyuan.update_document document_id="xxx" content="# 標題\n\n內容"
```

**✅ 最推薦：用 `content_file`（避免所有 shell 轉義問題）**
```bash
cat << 'EOF' > /tmp/content.md
# 標題

這是內容。

## 小節
- 項目 1
- 項目 2
EOF

mcporter call siyuan.update_document document_id="xxx" content_file="/tmp/content.md"
```

**✅ 其他可用寫法 1**：先寫入檔案再用 `$(cat ...)` 帶入
```bash
mcporter call siyuan.update_document document_id="xxx" content="$(cat /tmp/content.md)"
```

**✅ 其他可用寫法 2**：用 `$'...'` 語法（bash）
```bash
mcporter call siyuan.update_document document_id="xxx" content=$'# 標題\n\n這是內容。'
```

**✅ 其他可用寫法 3**：直接用多行引號
```bash
mcporter call siyuan.update_document document_id="xxx" content="# 標題

這是內容。

## 小節
- 項目 1
- 項目 2
"
```

### 其他注意事項

- 若工具列表讀不到，先確認：
  - `SIYUAN_TOKEN`、`SIYUAN_BASE_URL` 是否正確
  - 思源是否在該 baseUrl 啟動
  - mcporter 是否已設定 `siyuan`

- 若要看所有 tools：

```bash
mcporter list siyuan --all-parameters
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/purpleliu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

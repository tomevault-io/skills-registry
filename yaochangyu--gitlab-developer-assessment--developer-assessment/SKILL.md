---
name: developer-assessment
description: GitLab 開發者評估與分析技能。分析單一開發者、多位開發者或所有開發者的程式碼品質、commit 記錄、專案參與度、技術能力。支援時間區間篩選（過去一個月、三個月等）、專案範圍篩選（特定專案或全專案）。使用 gl-cli.py 工具產生綜合評估報告，包含 6 大維度評分、改善建議與學習方向。 Use when this capability is needed.
metadata:
  author: yaochangyu
---

# GitLab Developer Assessment Skill

GitLab 開發者評估與分析專家，透過 `gl-cli.py` 工具深度分析開發者在 GitLab 上的活動與貢獻，提供多維度的技術能力評估。

## 描述

本 Skill 專門處理開發者評估相關的任務，支援以下使用場景：

### 📊 支援的評估範圍
- **單一開發者分析**：`分析開發者 en20241119`
- **多位開發者分析**：`分析開發者 en20241119、G2023018、alice`
- **全體開發者分析**：`分析所有開發者`
- **彙整所有評估結果**：`彙整所有開發者的評估報告`

### 🎯 支援的篩選條件
- **時間區間**：過去一個月、過去三個月、2024-12-21 至 2026-01-21
- **專案範圍**：特定專案（如「新求才WebVue」）或全專案
- **評估類型**：完整評估、程式碼品質分析、協作能力評估、技術廣度評估

### 💡 使用範例

```
# 範例 1：單一開發者的完整評估
分析開發者 en20241119，過去一個月在「新求才WebVue」專案的完整評估

# 範例 2：多位開發者的比較分析
分析開發者 en20241119、G2023018，過去三個月的完整評估

# 範例 3：全體開發者的團隊分析
分析所有開發者，過去一個月的完整評估

# 範例 4：特定維度評估
分析開發者 alice 的程式碼品質和 commit 規範

# 範例 5：跨專案比較
分析開發者 bob 在所有專案的技術能力評估

# 範例 6：彙整所有評估報告
彙整所有開發者的評估報告成一份摘要表格
```

## 核心職責

1. **互動式需求確認**
   - 識別評估目標（單一、多位或全體開發者）
   - 解析時間區間（相對時間或絕對日期）
   - 確認專案範圍（特定專案或全專案）
   - 選擇評估維度（完整評估或特定維度）

2. **資料收集流程** (詳見 [gl-cli.py 完整操作手冊](../../../scripts/README.md))
   
   **標準工作流程**：
   ```
   Step 1: 執行 gl-cli.py user-details 收集開發者資料
   ↓
   Step 2: 掃描 .\scripts\output\users\{username}\ 目錄下所有 CSV 檔案
   ↓
   Step 3: 分析各項 CSV 檔案（commits, code_changes, merge_requests, code_reviews 等）
   ↓
   Step 4: 產生評估報告並儲存為 analysis-result.md
   ```

   **⚠️ 重要**：
   - 使用 `gl-cli.py user-details` 取得開發者詳細資訊（commits, code changes, MRs, reviews）
   - **輸出路徑固定為** `.\scripts\output\` **（不要使用 `--output` 參數）**
   - **索引檔案問題**：`{username}-index.md` 可能不完整！
     - 原因：索引檔案在每次執行時會被重新生成，只記錄當次匯出的檔案
     - 解決方案：**不要依賴 index.md，直接掃描目錄下的所有 CSV 檔案**
     - 正確做法：使用 `ls` 或 `glob` 列出 `.\scripts\output\users\{username}\*.csv` 所有檔案
   - 根據實際存在的 CSV 檔案進行深度分析（而非 index.md 列出的檔案）

3. **多維度評估** (詳見 [程式碼品質評估規範](./references/code-quality-analysis-spec.md))
   
   依據 **6 大評估維度** 進行分析（總分 100 分）：
   
   | 維度 | 權重 | 主要指標 | 資料來源 |
   |------|------|----------|---------|
   | **程式碼貢獻量** | 12% | 提交次數、活躍度、產出量 | statistics.csv, commits.csv |
   | **Commit 品質** | 23% | Message 規範、變更粒度、修復率 | commits.csv, code_changes.csv |
   | **技術廣度與深度** | 18% | 語言分布、檔案類型、技術棧 | code_changes.csv, statistics.csv |
   | **協作能力** | 12% | Merge Commits、衝突處理、Revert 率 | merge_requests.csv, commits.csv |
   | **Code Review 品質** | 10% | Review 參與度、深度、時效性 | code_reviews.csv, merge_requests.csv |
   | **工作模式與效率** | 10% | 時間分布、穩定性 | commits.csv (created_at) |
   | **進步趨勢** | 15% | 成長曲線、技能提升 | 時間序列對比 |

   **詳細評估方法請參考**：[code-quality-analysis-spec.md](./references/code-quality-analysis-spec.md)

4. **報告產生**
   - 產生結構化評估報告（markdown 格式）
   - **必須儲存為** `.\scripts\output\users\{username}\analysis-result.md`
   - 包含 6 大維度的綜合評分與詳細分析
   - 提供具體改善建議與學習方向

5. **彙整評估報告** (詳見 [彙整報告規範](#彙整報告功能))
   - 掃描 `.\scripts\output\users\*\analysis-result.md` 所有評估報告
   - 提取關鍵評估維度的分數
   - 產生 Markdown 表格格式的摘要報告
   - **必須儲存為** `.\scripts\output\users\all-user-analysis-result.md`

## 使用方式

### 在 GitHub Copilot Workspace 中使用

直接在對話中使用自然語言描述您的評估需求，本 Skill 會自動被觸發：

#### 範例 1：單一開發者、特定專案、特定時間
```
分析開發者 en20241119，過去一個月在「新求才WebVue」專案的完整評估
```
**執行流程**：
1. 解析參數：開發者=en20241119，專案=新求才WebVue，時間=過去一個月
2. 計算日期範圍（如 2024-12-21 至 2026-01-21）
3. 執行 `gl-cli.py user-details --username en20241119 --project-name "新求才WebVue" --start-date 2024-12-21 --end-date 2026-01-21`
4. 分析 CSV 資料產生評估報告

#### 範例 2：多位開發者比較分析
```
分析開發者 en20241119、G2023018，過去一個月在「新求才WebVue」專案的完整評估
```
**執行流程**：
1. 識別多位開發者：en20241119, G2023018
2. 分別收集兩位開發者的資料
3. 產生個別評估報告
4. 產生比較分析（可選）

#### 範例 3：全體開發者團隊分析
```
分析所有開發者，過去一個月的完整評估
```
**執行流程**：
1. 識別關鍵字「所有開發者」
2. 詢問是否指定專案範圍（或分析全專案）
3. 列出所有開發者清單
4. 批次執行評估並產生團隊摘要報告

#### 範例 4：特定維度分析
```
分析開發者 alice 的程式碼品質和 commit 規範
```
**執行流程**：
1. 識別評估維度：程式碼品質、commit 規範
2. 收集相關資料（commits.csv, code_changes.csv）
3. 僅針對指定維度進行深度分析

### 關鍵字觸發模式

本 Skill 會根據以下關鍵字模式自動觸發：

| 觸發模式 | 範例 | 說明 |
|---------|------|------|
| **分析開發者 + 名稱** | `分析開發者 en20241119` | 單一開發者評估 |
| **分析開發者 + 多個名稱** | `分析開發者 alice、bob、carol` | 多位開發者比較 |
| **分析所有開發者** | `分析所有開發者過去三個月` | 全體團隊分析 |
| **彙整評估報告** | `彙整所有開發者的評估報告` | 產生摘要表格 |
| **過去 X 個月/週** | `過去一個月`、`過去三個月` | 相對時間範圍 |
| **在「專案名稱」專案** | `在「新求才WebVue」專案` | 特定專案範圍 |
| **完整評估** | `完整評估`、`綜合評估` | 6 大維度完整分析 |
| **特定維度** | `程式碼品質`、`協作能力` | 單一維度深度分析 |

### 互動式確認流程

當使用者輸入的資訊不完整時，本 Skill 會主動詢問：

```
使用者：分析開發者 alice
↓
Skill：請問您要分析的時間範圍？
  1. 過去一個月
  2. 過去三個月
  3. 過去半年
  4. 自訂時間範圍
↓
使用者：選擇 1
↓
Skill：請問要分析哪些專案？
  1. 所有專案
  2. 指定專案（請提供專案名稱）
↓
使用者：選擇 2，專案名稱「新求才WebVue」
↓
Skill：開始執行評估...
```

## 工具整合

> 📚 **完整操作手冊**: [gl-cli.py 使用指南](../../../scripts/README.md) - 包含所有命令、參數說明與範例

### gl-cli.py 核心命令（v2.0.0+）

#### ⚠️ 命令執行規則與標準工作流程

**步驟 1：執行資料收集命令**

執行 `gl-cli.py user-details` 時，**不要使用 `--output` 參數**（使用預設輸出路徑）：

```bash
cd scripts

# ✅ 正確：使用預設輸出目錄 ./output
python3 gl-cli.py user-details \
  --username <開發者名稱> \
  --start-date <YYYY-MM-DD> \
  --end-date <YYYY-MM-DD>

# ❌ 錯誤：不要指定自訂輸出目錄
# python3 gl-cli.py user-details --username alice --output ./custom-dir
```

**步驟 2：掃描實際 CSV 檔案**

資料收集完成後，**不要依賴 index.md，直接掃描目錄**：

```bash
# 列出該開發者目錄下的所有 CSV 檔案
ls -lah .\scripts\output\users\<username>\*.csv

# 或使用 glob 模式
glob pattern="scripts/output/users/<username>/*.csv"
```

**步驟 3：分析 CSV 檔案**

根據目錄中實際存在的檔案進行深度分析：
- `statistics.csv` - 統計摘要（17 個指標）
- `commits.csv` - Commit 詳細記錄
- `code_changes.csv` - 程式碼異動詳情
- `merge_requests.csv` - MR 記錄
- `code_reviews.csv` - Code Review 評論
- 其他 CSV 檔案（根據實際產生的檔案）

**步驟 4：產生評估報告**

依據 [程式碼品質評估規範](./references/code-quality-analysis-spec.md) 產生報告，並儲存為：
```
.\scripts\output\users\<username>\analysis-result.md
```

**重要原因**：
- 後續分析流程依賴標準輸出路徑 `.\scripts\output\users\{username}\`
- 確保 CSV 檔案的路徑一致性
- 避免分析結果分散在不同目錄
- ⚠️ **index.md 可能不完整，請直接掃描目錄確認實際檔案**

---

```bash
# 環境設定
cd scripts

# ⭐ 主要命令：取得開發者詳細資訊（推薦使用）
python3 gl-cli.py user-details \
  --username <開發者名稱> \
  --project-name <專案名稱> \
  --start-date <YYYY-MM-DD> \
  --end-date <YYYY-MM-DD>

# 輸出檔案結構：
# .\scripts\output\                   # 預設輸出目錄（可透過 --output 參數自訂）
# ├── users\                          # 開發者資料目錄
# │   └── {username}\                 # 每位開發者獨立目錄
# │       ├── {username}-index.md     # 索引檔案（資料摘要與檔案清單）
# │       ├── user_profile.csv        # 使用者基本資料 (30+ 欄位)
# │       ├── user_events.csv         # 活動事件追蹤
# │       ├── commits.csv             # Commit 詳細記錄
# │       ├── code_changes.csv        # 程式碼異動詳情（file-level diffs）
# │       ├── merge_requests.csv      # MR 記錄
# │       ├── code_reviews.csv        # Code Review 評論
# │       ├── contributors.csv        # 專案貢獻者統計
# │       ├── permissions.csv         # 專案授權資訊
# │       └── statistics.csv          # 統計摘要 (多項指標)
# ├── groups\                         # 群組資料目錄
# │   └── {groupname}\
# │       ├── groups.csv              # 群組資訊
# │       ├── subgroups.csv           # 子群組列表
# │       ├── projects.csv            # 專案列表
# │       ├── permissions.csv         # 成員權限
# │       └── summary.csv             # 群組摘要
# └── projects\                       # 專案資料目錄
#     └── {projectname}\
#         ├── project.csv             # 專案資訊
#         └── permissions.csv         # 專案權限
# 
# 📌 CSV 檔案編碼：UTF-8 with BOM (utf-8-sig)，Excel 可直接開啟
# 
# 範例：分析開發者 G2023018 在 "新求才WebVue" 專案
# → 輸出路徑：.\scripts\output\users\G2023018\commits.csv

# 取得使用者專案列表
python3 gl-cli.py user-projects \
  --username <開發者名稱> \
  --group-name <群組名稱> \
  --output <輸出目錄>  # 選填

# 取得專案詳細資訊
python3 gl-cli.py project-stats \
  --project-name <專案名稱> \
  --output <輸出目錄>  # 選填

# 取得群組資訊
python3 gl-cli.py group-stats \
  --group-name <群組名稱> \
  --output <輸出目錄>  # 選填
```

### 🆕 支援多參數查詢與批次模式優化

```bash
# 多位使用者同時查詢（自動啟用批次模式優化）
# 批次模式：預先載入專案清單，共用快取，顯著提升效能
python3 gl-cli.py user-details \
  --username alice bob charlie \
  --start-date 2024-01-01

# 多個專案同時查詢（笛卡爾積模式）
python3 gl-cli.py user-details \
  --project-name "web-api" "mobile-app" "admin-panel" \
  --start-date 2024-01-01

# 組合查詢：多位使用者在多個專案的活動
# 注意：多使用者 + 多專案 → 使用笛卡爾積模式（較慢）
python3 gl-cli.py user-details \
  --username alice bob \
  --project-name "web-api" "mobile-app" \
  --start-date 2024-01-01

# 💡 效能建議：
# ✅ 推薦：多使用者 + 單一專案範圍（或不指定專案）→ 批次模式
# ⚠️  較慢：多使用者 + 多專案 → 笛卡爾積模式
```

### 相依套件與環境設定

執行前需確保已安裝：
- pandas
- openpyxl
- urllib3
- python-gitlab >= 4.4.0 (透過 gitlab_client.py)

若缺少套件，引導使用者安裝：
```bash
cd scripts
source .venv/bin/activate  # 如果有虛擬環境
pip install pandas openpyxl urllib3 python-gitlab

# 或使用 uv（推薦）
cd scripts && uv sync
```

### 設定檔配置

首次使用需設定 GitLab 連線資訊：
```bash
# 複製範本
cp scripts/config-example.py scripts/config.py

# 編輯 config.py，設定以下參數：
# - GITLAB_URL: GitLab 伺服器位址
# - GITLAB_TOKEN: Personal Access Token（需要 read_api, read_repository, read_user 權限）
# - START_DATE / END_DATE: 預設分析時間範圍
# - TARGET_GROUP_ID: 預設群組 ID（可選）
```

## 互動式工作流程

### 第 1 步：需求確認

詢問使用者：

1. **分析對象**
   - 特定開發者（請提供 GitLab username）
   - 整個團隊/群組（請提供群組名稱或 ID）
   - 所有開發者（取得所有使用者列表）

2. **時間範圍**
   - 最近 1 個月
   - 最近 3 個月
   - 最近 6 個月
   - 自訂時間區間（請提供開始與結束日期）

3. **專案範圍**
   - 所有專案
   - 特定專案（請提供專案名稱列表）
   - 特定群組下的專案（請提供群組 ID 或名稱）

4. **評估維度**（多選）
   - [ ] 提交活躍度與頻率
   - [ ] 程式碼變更量分析
   - [ ] Commit message 品質
   - [ ] 專案參與廣度
   - [ ] 技術棧識別
   - [ ] 完整評估（包含所有維度）

### 第 3 步：資料分析

**分析流程**（依據 [程式碼品質評估規範](./references/code-quality-analysis-spec.md)）：

#### 3.0 掃描目錄確認可用檔案

**⚠️ 第一步：掃描目錄確認實際存在的 CSV 檔案！**

```bash
# 掃描使用者目錄下的所有 CSV 檔案
ls -lah ./output/users/<username>/*.csv

# 或使用更友善的顯示方式
find ./output/users/<username>/ -name "*.csv" -type f -printf "%f\n"
```

**從目錄掃描結果確認**：
- 📁 哪些資料檔案可用（commits.csv、code_changes.csv 等）
- 📊 檔案大小（判斷是否有實質資料）
- 🕒 修改時間（確認資料時效性）

---

#### 3.1 程式碼貢獻量分析（權重 12%）

**資料來源**：`statistics.csv`、`commits.csv`

**分析指標**：
- ✅ 提交次數（commits_count）
- ✅ 活躍天數（active_days）
- ✅ 程式碼新增/刪除行數（total_additions, total_deletions）
- ✅ 涉及檔案數（files_changed_count）

**評分標準**：
- 🏆 200+ 次提交：高活躍度 (9-10分)
- ⭐ 100-200 次：穩定貢獻 (7-8分)
- 📚 50-100 次：中等參與 (5-6分)
- 🌱 <50 次：參與度低 (1-4分)

**分析命令**：
```bash
# 讀取統計摘要
cat ./output/users/<username>/statistics.csv | grep "commits_count\|active_days\|total_additions"
```

---

#### 3.2 Commit 品質分析（權重 23%）⭐ **最重要**

**資料來源**：`commits.csv`、`code_changes.csv`

**分析維度**：

##### A. Message 規範性（30%）

**檢查指標**：
- 是否遵循 Conventional Commits 格式（feat, fix, docs, refactor, test, chore, style, perf）
- Message 長度是否適中（50-72 字元）
- 是否包含清楚的描述

**優質範例**：
```
feat(auth): add JWT token validation
fix(api): resolve null pointer in user service
docs(readme): update installation guide
refactor(utils): extract common validation logic
```

**劣質範例**：
```
update
fix bug
修改
WIP
```

**評分標準**：
- ✅ 規範率 >80%：優秀 (9-10分)
- ⚠️ 規範率 40-80%：中等 (5-8分)
- ❌ 規範率 <40%：需改進 (1-4分)

**分析命令**：
```bash
# 檢查 commits.csv 中的 message 欄位
# 統計符合 Conventional Commits 的比例
grep -E "^(feat|fix|docs|refactor|test|chore|style|perf)\(" ./output/users/<username>/commits.csv | wc -l
```

---

##### B. 變更粒度（40%）

**檢查指標**：單次 commit 的程式碼變更量

**黃金比例**：
| 規模 | 行數 | 理想佔比 | 說明 |
|------|------|----------|------|
| 小型 | ≤100 行 | **60%+** | 模組化思維好 ✅ |
| 中型 | 100-500 行 | 30% | 正常功能開發 |
| 大型 | >500 行 | <10% | 過多表示缺乏拆分能力 ⚠️ |

**評分標準**：
- 小型變更佔比 >60%：優秀 (9-10分)
- 小型變更佔比 40-60%：良好 (6-8分)
- 小型變更佔比 <40%：需改進 (1-5分)

**分析命令**：
```bash
# 計算每次 commit 的變更量（additions + deletions）
# 統計小型/中型/大型變更的比例
cat ./output/users/<username>/commits.csv | awk -F',' '{print $additions + $deletions}'
```

---

##### C. 修復性提交比例（30%）

**檢查指標**：包含修復關鍵字的提交比例

**關鍵字**：fix, bug, hotfix, revert

**評分標準**：
- ✅ 修復率 <15%：程式碼品質高 (9-10分)
- ⚠️ 修復率 15-30%：正常範圍 (5-8分)
- ❌ 修復率 >30%：品質問題或測試不足 (1-4分)

**分析命令**：
```bash
# 統計包含修復關鍵字的提交
grep -iE "fix|bug|hotfix|revert" ./output/users/<username>/commits.csv | wc -l
```

---

#### 3.3 技術廣度與深度分析（權重 18%）

**資料來源**：`code_changes.csv`、`statistics.csv`

**分析指標**：
- 檔案類型分布（識別技術棧）
- 程式語言使用比例
- 前端 vs 後端 vs 全棧
- DevOps/Infrastructure 能力

**檔案類型對照表**：

**前端開發者**：
```
.js, .ts, .jsx, .tsx     → React/Vue/Angular
.css, .scss, .sass       → 樣式設計
.html, .cshtml           → 模板
```

**後端開發者**：
```
.cs                      → C# .NET
.java                    → Java Spring
.py                      → Python Django/Flask
.go                      → Golang
```

**DevOps/SRE**：
```
Dockerfile               → 容器化
.yml, .yaml              → CI/CD
.sh, .bash               → 自動化腳本
.tf                      → Terraform (基礎設施即程式碼)
```

**評分標準**：
- 🏆 5+ 種語言：技術廣度優秀 (9-10分)
- ⭐ 3-5 種：全棧能力 (7-8分)
- 📚 1-2 種：專精型 (5-6分)

**分析命令**：
```bash
# 統計檔案類型分佈
cat ./output/users/<username>/code_changes.csv | \
  awk -F',' '{print $file_path}' | \
  sed 's/.*\(\.[^.]*\)$/\1/' | \
  sort | uniq -c | sort -rn
```

---

#### 3.4 協作能力分析（權重 12%）

**資料來源**：`merge_requests.csv`、`commits.csv`

**分析維度**：

##### A. Merge Commits（協作參與度）

**指標**：
- 有 merge commits → 表示參與分支協作
- 無 merge commits → 可能獨立開發或使用 rebase

##### B. 被 Revert 的次數（程式碼穩定性）

**評分標準**：
- ✅ Revert 率 <2%：優秀
- ⚠️ Revert 率 2-5%：正常
- ❌ Revert 率 >5%：需改進

**分析命令**：
```bash
# 查找被回退的提交
grep -i "revert" ./output/users/<username>/commits.csv | wc -l
```

---

#### 3.5 Code Review 品質分析（權重 10%）⭐ **新增維度**

**資料來源**：`code_reviews.csv`、`merge_requests.csv`

**分析維度**（詳見 [code-quality-analysis-spec.md](./references/code-quality-analysis-spec.md) 第 5 節）：

##### A. Review 參與度（30%）

**指標**：
- Review 數量（作為 Reviewer 的 MR 數量）
- Review Comments 總數
- 與團隊平均的比較

**評分標準**：
- 🏆 Review 數量 > 團隊平均 1.5 倍：優秀 (9-10分)
- ⭐ Review 數量 = 團隊平均：良好 (7-8分)
- 📚 Review 數量 = 團隊平均 50-100%：中等 (5-6分)
- 🌱 Review 數量 < 團隊平均 50%：需改進 (1-4分)

---

##### B. Review 深度（40%）⭐ **最重要**

**優質 Review Comment 範例**：
```markdown
這裡的 SQL 查詢可能有 N+1 問題，建議改用 JOIN：

// 目前寫法（會產生 100 次查詢）
const users = await User.findAll();
for (const user of users) {
  user.posts = await Post.findAll({ where: { userId: user.id } });
}

// 建議修改（只需 1 次查詢）
const users = await User.findAll({
  include: [{ model: Post }]
});
```

**劣質 Review Comment 範例**：
```markdown
LGTM
看起來不錯
👍
沒問題
```

**評分標準**：
- ✅ 80%+ 的 Review 包含具體建議：優秀 (9-10分)
- ⚠️ 50-80% 有建議：中等 (5-8分)
- ❌ <50% 只是 LGTM/Approve：需改進 (1-4分)

**發現問題的嚴重等級**：
- Critical Issues（安全漏洞、資料遺失風險）：+5 分
- Major Issues（效能問題、邏輯錯誤）：+3 分
- Minor Issues（程式碼風格、命名）：+1 分

---

##### C. Review 時效性（20%）

**指標**：平均 Review 回應時間

**評分標準**：
- ⚡ < 4 小時：優秀 (9-10分)
- ⭐ 4-24 小時：良好 (7-8分)
- 📚 24-72 小時：普通 (5-6分)
- 🐌 > 72 小時：阻礙開發流程 (1-4分)

---

##### D. 被 Review 的接受度（10%）

**指標**：
- 被 Request Changes 的比例
- 需要二次 Review 的比例

**評分標準**：
- ✅ Request Changes 率 < 15%：程式碼品質高 (9-10分)
- ⚠️ Request Changes 率 15-30%：正常範圍 (7-8分)
- ❌ Request Changes 率 > 30%：品質需改進 (1-6分)

**分析命令**：
```bash
# 分析 code_reviews.csv
cat ./output/users/<username>/code_reviews.csv | \
  grep -v "^discussion_id" | \
  wc -l  # Review Comments 總數
```

---

#### 3.6 工作模式與效率分析（權重 10%）

**資料來源**：`commits.csv`（created_at 欄位）

**分析指標**：
- 時間分佈（星期分佈、小時分佈）
- 工作時段提交率
- 穩定性

**優質工作模式**：
- ✅ 工作日集中（週一到週五）
- ✅ 工作時段 (9-18點) 提交率 >60%
- ✅ 每天穩定多次小 commit

**警訊模式**：
- ⚠️ 深夜/凌晨頻繁提交 → 時間管理問題
- ⚠️ 週末集中爆量 → 可能趕工
- ⚠️ 不規律提交 → deadline 驅動

**分析命令**：
```bash
# 分析提交時間分佈
cat ./output/users/<username>/commits.csv | \
  awk -F',' '{print $created_at}' | \
  awk -F'T' '{print $2}' | \
  awk -F':' '{print $1}' | \
  sort | uniq -c
```

---

#### 3.7 進步趨勢分析（權重 15%）

**分析方法**：時間切片對比

**比較維度**：
- ✅ Commit message 品質提升
- ✅ 單次變更規模更合理
- ✅ 技術棧擴展
- ✅ 修復率降低
- ✅ Code Review 參與度提升

**分析策略**：
```bash
# 將資料分為兩個時間段
# 早期表現（前 50% 資料）vs 近期表現（後 50% 資料）
# 比較各項指標的變化趨勢
```

---

### 🎯 綜合評分計算

**總分公式**（滿分 10 分）：

```
總分 = (貢獻量得分 × 0.12) +
       (Commit 品質得分 × 0.23) +
       (技術廣度得分 × 0.18) +
       (協作得分 × 0.12) +
       (Code Review 得分 × 0.10) +
       (工作模式得分 × 0.10) +
       (進步趨勢得分 × 0.15)
```

**分級標準**：

| 等級 | 分數 | 特徵描述 |
|------|------|----------|
| 🏆 **高級工程師** | 8-10 | • Message 規範率 90%+<br>• 小型變更佔比 80%+<br>• 涉及 3+ 技術棧<br>• 修復率 <15%<br>• 有架構級別變更<br>• **Review 參與度高，能發現 Critical Issues** ⭐ |
| ⭐ **中級工程師** | 5-7 | • Message 規範率 60-90%<br>• 變更粒度合理<br>• 2-3 種技術棧<br>• 修復率 15-30%<br>• 功能開發為主<br>• **有 Code Review 參與但深度一般** |
| 🌱 **初級工程師** | 1-4 | • Message 不規範<br>• 大量修復性提交<br>• 單一技術棧<br>• 變更集中於小範圍<br>• **Code Review 參與少或僅 LGTM** |

---

### 第 4 步：產生評估報告

**⚠️ 重要：分析報告必須輸出到指定位置！**

**輸出規則**：
- 📁 **輸出目錄**：`.\scripts\output\users\{username}`
- 📄 **檔名**：`analysis-result.md`
- 📝 **完整路徑範例**：`.\scripts\output\users\john.doe\analysis-result.md`

**報告結構**（依據 [code-quality-analysis-spec.md](./references/code-quality-analysis-spec.md)）：

```markdown
# GitLab 開發者評估報告

## 📊 基本資訊
- **開發者**: <username>
- **分析期間**: <start_date> ~ <end_date>
- **分析專案**: <project_count> 個專案
- **報告生成時間**: <timestamp>
- **資料來源**: GitLab API (透過 gl-cli.py v2.0.0+)

---

## 🎯 綜合評分（滿分 10 分）

### 總分：⭐⭐⭐⭐☆ (X.X/10)
**等級**：🏆 高級工程師 / ⭐ 中級工程師 / 🌱 初級工程師

### 詳細評分表

| 評估維度 | 權重 | 得分 | 評級 | 說明 |
|---------|------|------|------|------|
| **程式碼貢獻量** | 12% | X.X/10 | ⭐⭐⭐⭐☆ | XXX 次提交，活躍度高 |
| **Commit 品質** | 23% | X.X/10 | ⭐⭐⭐⭐⭐ | Message 規範率 XX%，變更粒度合理 |
| **技術廣度與深度** | 18% | X.X/10 | ⭐⭐⭐⭐☆ | 涉及 X 種技術棧，全棧能力 |
| **協作能力** | 12% | X.X/10 | ⭐⭐⭐☆☆ | 參與協作，Revert 率低 |
| **Code Review 品質** | 10% | X.X/10 | ⭐⭐⭐⭐☆ | Review 參與度高，深度中等 |
| **工作模式與效率** | 10% | X.X/10 | ⭐⭐⭐⭐⭐ | 工作時段穩定，效率良好 |
| **進步趨勢** | 15% | X.X/10 | ⭐⭐⭐⭐☆ | 各項指標穩定提升 |

**計算公式**：
```
總分 = (X.X × 0.12) + (X.X × 0.23) + (X.X × 0.18) + 
       (X.X × 0.12) + (X.X × 0.10) + (X.X × 0.10) + (X.X × 0.15) = X.X
```

---

## 1️⃣ 程式碼貢獻量分析（權重 12%）

### 關鍵指標
- **總提交次數**：XXX 次
- **活躍天數**：XXX 天（佔比 XX%）
- **程式碼新增**：+XXX 行
- **程式碼刪除**：-XXX 行
- **涉及檔案數**：XXX 個

### 評估結果
- ✅ **活躍度評分**：X.X/10
- 📊 **與團隊平均比較**：高於/等於/低於平均 XX%
- 📈 **趨勢**：穩定/上升/下降

### 詳細分析
[根據 statistics.csv 與 commits.csv 的數據，詳細說明開發者的貢獻量表現]

---

## 2️⃣ Commit 品質分析（權重 23%）⭐ **核心指標**

### A. Message 規範性（30%）
- **規範率**：XX% (符合 Conventional Commits)
- **評分**：X.X/10
- **範例優質 Commit**：
  ```
  feat(auth): add JWT token validation
  fix(api): resolve null pointer in user service
  ```

### B. 變更粒度（40%）
- **小型變更（≤100行）**：XX% (理想：60%+)
- **中型變更（100-500行）**：XX%
- **大型變更（>500行）**：XX% (警示：>10%)
- **評分**：X.X/10

### C. 修復性提交比例（30%）
- **修復率**：XX% (包含 fix, bug, hotfix, revert)
- **評分**：X.X/10
- **分析**：修復率<15%表示程式碼品質高

### 綜合評分：X.X/10

---

## 3️⃣ 技術廣度與深度分析（權重 18%）

### 技術棧分布
| 技術類型 | 涉及檔案類型 | 佔比 | 熟練度 |
|---------|-------------|------|--------|
| 前端開發 | .js, .ts, .jsx, .tsx | XX% | ⭐⭐⭐⭐⭐ |
| 後端開發 | .cs, .java, .py | XX% | ⭐⭐⭐⭐☆ |
| 樣式設計 | .css, .scss | XX% | ⭐⭐⭐☆☆ |
| DevOps | Dockerfile, .yml | XX% | ⭐⭐☆☆☆ |

### 程式語言分布
1. **TypeScript**：XX%
2. **C#**：XX%
3. **Python**：XX%
4. **其他**：XX%

### 專長領域判定
- ✅ **主要領域**：後端開發（C#/Python）
- ✅ **次要領域**：前端開發（TypeScript/React）
- 🔄 **學習中**：DevOps/容器化

### 評分：X.X/10
- 🏆 技術廣度：涉及 X 種語言（5+ 種為優秀）
- 📚 技術深度：在 X 個領域有深入貢獻

---

## 4️⃣ 協作能力分析（權重 12%）

### Merge Requests 參與度
- **總 MR 數量**：XXX 個
- **已合併**：XXX 個 (XX%)
- **平均合併時間**：X.X 天

### 協作模式
- **Merge Commits**：XXX 次（表示參與分支協作）
- **Conflict 處理**：XXX 次
- **被 Revert 次數**：XXX 次（Revert 率：XX%）

### 評分：X.X/10
- ✅ Revert 率 <2%：優秀
- ⚠️ 需改進：提升 MR 合併速度

---

## 5️⃣ Code Review 品質分析（權重 10%）

### A. Review 參與度（30%）
- **Review 總數**：XXX 次
- **Review Comments**：XXX 條
- **與團隊平均比較**：高於/等於/低於平均 XX%
- **評分**：X.X/10

### B. Review 深度（40%）⭐
- **有建議的 Review 比例**：XX%
- **發現的問題分類**：
  - Critical Issues（安全、資料遺失）：XXX 個
  - Major Issues（效能、邏輯錯誤）：XXX 個
  - Minor Issues（風格、命名）：XXX 個
- **評分**：X.X/10

### C. Review 時效性（20%）
- **平均回應時間**：X.X 小時
- **評分**：X.X/10
- **分析**：<4小時為優秀，>72小時會阻礙流程

### D. 被 Review 的接受度（10%）
- **Request Changes 率**：XX%
- **評分**：X.X/10

### 綜合評分：X.X/10

---

## 6️⃣ 工作模式與效率分析（權重 10%）

### 時間分布
**星期分佈**：
- 週一至週五：XX%
- 週末：XX%

**小時分佈**：
- 工作時段（9-18點）：XX%
- 非工作時段：XX%

### 工作模式評估
- ✅ **穩定性**：工作日集中，時間分佈合理
- ⚠️ **警訊**：深夜提交佔比 XX%（建議改善時間管理）

### 評分：X.X/10

---

## 7️⃣ 進步趨勢分析（權重 15%）

### 時間序列對比

| 指標 | 早期表現 | 近期表現 | 變化趨勢 |
|------|---------|---------|---------|
| Commit 規範率 | XX% | XX% | ↗️ 提升 XX% |
| 平均變更粒度 | XXX 行 | XXX 行 | ↘️ 改善 XX% |
| 技術棧數量 | X 種 | X 種 | ↗️ 增加 X 種 |
| 修復率 | XX% | XX% | ↘️ 降低 XX% |
| Code Review 參與 | XXX 次 | XXX 次 | ↗️ 提升 XX% |

### 進步亮點
1. ✅ **Commit 品質提升**：Message 規範率從 XX% 提升至 XX%
2. ✅ **技術棧擴展**：新增 X 種技術棧（列出）
3. ✅ **Code Review 深度提升**：發現 Critical Issues 數量增加

### 評分：X.X/10

---

## 💡 改善建議

### 🔥 優先改善項目（高優先級）
1. **[具體問題]**
   - **現況**：XXX
   - **目標**：XXX
   - **建議行動**：XXX

2. **[具體問題]**
   - **現況**：XXX
   - **目標**：XXX
   - **建議行動**：XXX

### ⭐ 持續優化項目（中優先級）
1. **提升技術廣度**
   - 建議學習：XXX
   - 建議專案：XXX

2. **深化 Code Review 參與**
   - 目標：每週 Review XXX 個 MR
   - 重點：發現架構/效能問題

### 📚 長期發展方向
1. **技術領導力**
   - 承擔更多架構設計工作
   - 帶領技術分享與知識傳承

2. **跨團隊協作**
   - 參與更多跨專案協作
   - 提升影響力範圍

---

## 📌 數據來源

本報告資料來源於 GitLab API，透過 `gl-cli.py v2.0.0+` 工具收集：

- **使用者基本資料**：`user_profile.csv`
- **提交記錄**：`commits.csv` (XXX 筆)
- **程式碼變更**：`code_changes.csv` (XXX 筆)
- **Merge Requests**：`merge_requests.csv` (XXX 筆)
- **Code Reviews**：`code_reviews.csv` (XXX 筆)
- **統計摘要**：`statistics.csv`

**分析期間**：<start_date> ~ <end_date>

**評估標準**：[程式碼品質評估規範](../../../.copilot/skills/developer-assessment/references/code-quality-analysis-spec.md)

---

**報告結束** | 產生時間：<timestamp>
```

**儲存報告**：
```bash
# 將上述 Markdown 報告儲存到指定位置
# 路徑格式：.\scripts\output\users\{username}\analysis-result.md
cat > ./output/users/<username>/analysis-result.md << 'EOF'
[將上述報告內容貼上]
EOF

# 確認檔案已成功建立
ls -lh ./output/users/<username>/analysis-result.md
```

**範例**：
```bash
# 為開發者 john.doe 產生分析報告
# 輸出路徑：.\scripts\output\users\john.doe\analysis-result.md
cat > ./output/users/john.doe/analysis-result.md << 'EOF'
# GitLab 開發者評估報告

## 📊 基本資訊
- **開發者**: john.doe
- **分析期間**: 2025-10-20 ~ 2026-01-20
- **分析專案**: 5 個專案
- **報告生成時間**: 2026-01-21 14:30:00
...
EOF

# 輸出：./output/users/john.doe/analysis-result.md (XX KB)
```

---

## 💻 程式碼貢獻分析

- **總變更行數**: +X / -Y 行
- **平均每次 Commit**: ~Z 行
- **主要變更類型**: 
  - 新增功能: X%
  - Bug 修復: Y%
  - 重構: Z%

### Commit Message 品質
- ✅ 規範性: 良好
- ✅ 描述性: 詳細
- ⚠️ 改善建議: 建議增加 ticket ID 引用

---

## 🚀 專案參與度

| 專案名稱 | Commit 數 | 貢獻度 | 角色 |
|---------|----------|--------|------|
| Project A | X | High | Core Developer |
| Project B | Y | Medium | Contributor |
| Project C | Z | Low | Occasional |

**核心專案**: Project A (貢獻度 X%)

---

## 🔧 技術棧分析

### 程式語言分布
- Python: X%
- JavaScript: Y%
- SQL: Z%
- Others: W%

### 專長領域
- ✅ 後端開發 (Python, API)
- ✅ 資料庫設計 (SQL)
- 🔄 前端開發 (JavaScript) - 持續學習中

---

## 💡 改善建議

1. **提升專案參與廣度**
   - 建議參與更多跨團隊協作專案
   - 嘗試不同領域的技術挑戰

2. **深化技術深度**
   - 在核心專案中承擔更多架構設計工作
   - 增加 code review 參與度

3. **知識分享**
   - 建議撰寫技術文件或 README
   - 參與 issue 討論，分享經驗

---

## 📌 數據來源

本報告資料來源於 GitLab，透過 `gl-cli.py` 工具收集：
- 使用者統計: `user-stats`
- 專案列表: `user-projects`
- 時間範圍: <start_date> ~ <end_date>

---

**報告結束**
\`\`\`

**儲存報告**：
```bash
# 將上述 Markdown 報告儲存到指定位置
# 路徑格式：.\scripts\output\users\{username}\analysis-result.md
cat > .\scripts\output\users\<username>\analysis-result.md << 'EOF'
[將上述報告內容貼上]
EOF

# 確認檔案已成功建立
ls -lh .\scripts\output\users\<username>\analysis-result.md
```

**範例**：
```bash
# 為開發者 john.doe 產生分析報告
# 輸出路徑：.\scripts\output\users\john.doe\analysis-result.md
cat > .\scripts\output\users\john.doe\analysis-result.md << 'EOF'
# GitLab Developer Assessment Report

## 📊 基本資訊
- **開發者**: john.doe
- **分析期間**: 2024-10-01 ~ 2026-01-20
...
EOF

# 輸出：.\scripts\output\users\john.doe\analysis-result.md (25KB)
```

### 第 5 步：後續行動建議

詢問使用者是否需要：

1. **查看報告與資料**
   - 分析報告已儲存在：`.\scripts\output\users\{username}\analysis-result.md`
   - 原始 CSV 資料位於：`.\scripts\output\users\{username}\*.csv`
   - 是否需要額外的圖表或統計分析？

2. **進階分析**
   - 與團隊平均值比較
   - 時間序列趨勢分析
   - 特定專案深度分析

3. **持續追蹤**
   - 設定定期評估機制
   - 建立績效追蹤儀表板

## 錯誤處理

### 常見問題處理

1. **缺少相依套件**
   ```bash
   # 引導安裝
   cd scripts
   source .venv/bin/activate  # 如果有虛擬環境
   pip install pandas openpyxl urllib3 python-gitlab
   
   # 或使用 uv（推薦）
   cd scripts && uv sync
   ```

2. **GitLab 連線失敗**
   - 檢查 `scripts/config.py` 設定（GITLAB_URL, GITLAB_TOKEN）
   - 確認 GitLab Token 有效性與權限（需要 read_api, read_repository, read_user）
   - 驗證網路連線與 SSL 憑證
   - SSL 憑證問題：gl-cli.py 預設已停用 SSL 驗證（ssl_verify=False）

3. **無資料返回**
   - 確認使用者名稱正確
   - 檢查時間範圍是否合理
   - 驗證使用者在指定專案中有活動

4. **權限不足**
   - 確認 GitLab Token 有足夠權限（read_api, read_repository）
   - 檢查使用者是否為專案成員

## 最佳實踐

1. **資料保護**
   - 輸出檔案預設儲存在 `.\scripts\output\`，可透過 `--output` 自訂
   - 不在報告中包含敏感資訊（email、token）
   - 分析完成後詢問是否刪除暫存檔案

2. **效能優化**
   - ✅ **批次模式**：多位使用者 + 單一專案範圍（或不指定專案）→ 自動啟用批次模式，預先載入專案清單，共用快取
   - ⚠️ **笛卡爾積模式**：多位使用者 + 多個專案 → 效能較慢，建議拆分查詢
   - 大型團隊分析時，建議分批處理（利用批次模式優化）
   - 使用 `--group-id` 限縮查詢範圍
   - 避免過長的時間區間（建議 ≤ 6 個月）
   - gl-cli.py 使用多執行緒（ThreadPoolExecutor）加速專案分析，最多 10 個並行執行緒

3. **報告品質**
   - 優先使用 `statistics.csv` 取得統計摘要，快速掌握整體表現
   - 提供具體數據支持評分
   - 避免主觀判斷，基於客觀指標
   - 給予建設性改善建議
   - **必須將報告儲存為** `.\scripts\output\users\{username}\analysis-result.md`

4. **使用者體驗**
   - 清晰的互動提示
   - 進度即時反饋（多查詢時自動顯示進度：查詢 X/Y）
   - 結果易於理解的視覺化呈現
   - CSV 使用 UTF-8 BOM 編碼，Excel 可直接開啟無亂碼
   - **分析報告必須儲存在**：`.\scripts\output\users\{username}\analysis-result.md`

## 範例對話流程

```
使用者: 幫我分析開發者 john.doe 的表現

Skill: 好的！我將協助您分析 john.doe 的開發表現。請回答以下問題：

1. 分析時間範圍？
   a) 最近 1 個月
   b) 最近 3 個月
   c) 最近 6 個月
   d) 自訂（請提供起迄日期）

使用者: b

Skill: 2. 專案範圍？
   a) 所有專案
   b) 特定專案（請列出專案名稱）
   c) 特定群組（請提供群組 ID）

使用者: a

Skill: 3. 評估維度？（可多選）
   a) 提交活躍度
   b) 程式碼品質
   c) 專案參與度
   d) 技術棧分析
   e) 完整評估（全部）

使用者: e

Skill: 收到！開始執行完整評估...

[步驟 1：檢查使用者資料目錄]
Skill: 🔍 檢查 john.doe 的資料目錄是否存在...
       路徑：.\scripts\output\users\john.doe\

[情境 A：資料目錄已存在且有 CSV 檔案]
Skill: ✅ 資料目錄已存在，掃描 CSV 檔案...
       📁 找到 7 個 CSV 檔案：commits.csv, code_changes.csv, merge_requests.csv...
       ⏭️  直接進行分析流程

[情境 B：資料目錄不存在或 CSV 檔案不完整]
Skill: ❌ 資料不存在或不完整
       📥 開始收集 john.doe 的資料...
       [執行 gl-cli.py user-details --username john.doe ...]
       ✅ 資料收集完成！

[步驟 2：掃描確認可用的 CSV 檔案]
Skill: 📖 掃描目錄確認可用的 CSV 檔案...
       [執行 ls -lah .\scripts\output\users\john.doe\*.csv]

Skill: 根據掃描結果，john.doe 在過去 3 個月有：
       - 📊 commits.csv (144KB) - 提交記錄
       - 📝 code_changes.csv (20MB) - 程式碼變更詳情
       - 🔀 merge_requests.csv (63KB) - Merge Request
       - 👀 code_reviews.csv (186KB) - Code Review 記錄
       - 📈 statistics.csv (552B) - 統計摘要

現在進行深入分析...

[讀取並分析 statistics.csv、commits.csv、code_reviews.csv 等檔案]
[根據資料產生評估報告]

完成！評估報告已產生：

📄 報告位置：`.\scripts\output\users\john.doe\analysis-result.md`

[顯示報告內容摘要或完整報告]

是否需要：
- 查看完整報告內容？
- 查看詳細 CSV 資料？
- 與團隊平均值比較？
- 進行特定面向的深入分析？
```

## 整合其他工具

若需要進階分析，可整合：

1. **Git 本地分析** (若有 clone repository)
   - 使用 `git log` 分析 commit 歷史
   - 使用 `git blame` 分析程式碼擁有權
   - 使用 `git diff` 分析變更品質

2. **程式碼品質工具**
   - pylint / flake8 (Python)
   - eslint (JavaScript)
   - SonarQube

3. **視覺化工具**
   - matplotlib / seaborn (Python 圖表)
   - Excel 樞紐分析表
   - Grafana (若有 metrics)

## 彙整報告功能

### 功能描述

當需要查看所有開發者的評估摘要時，此功能會：
1. 掃描 `.\scripts\output\users\` 目錄下所有開發者的 `analysis-result.md`
2. 提取每位開發者的關鍵評估維度分數
3. 產生 Markdown 表格格式的彙整報告
4. 儲存至 `.\scripts\output\users\all-user-analysis-result.md`

### 彙整報告欄位

| 欄位名稱 | 說明 | 資料來源 |
|---------|------|---------|
| **username** | 開發者帳號 | 從目錄名稱或報告中提取 |
| **程式碼貢獻量** | 程式碼貢獻量評分 (X.XX/10) | analysis-result.md 中的「程式碼貢獻量」維度分數 |
| **技術廣度** | 技術廣度評分 (X.XX/10) | analysis-result.md 中的「技術廣度」維度分數 |
| **協作能力** | 協作能力評分 (X.XX/10) | analysis-result.md 中的「協作能力」維度分數 |
| **Code Review 品質** | Code Review 品質評分 (X.XX/10) | analysis-result.md 中的「Code Review 品質」維度分數 |
| **工作模式** | 工作模式評分 (X.XX/10) | analysis-result.md 中的「工作模式」維度分數 |
| **進步趨勢** | 進步趨勢評分 (X.XX/10) | analysis-result.md 中的「進步趨勢」維度分數 |

### 輸出格式範例

```markdown
# 開發者評估彙整報告

**生成時間：** 2026-01-21 12:30:13  
**總開發者數：** 5

## 📊 綜合評估表

| username | 程式碼貢獻量 | 技術廣度 | 協作能力 | Code Review 品質 | 工作模式 | 進步趨勢 |
|----------|-------------|---------|---------|-----------------|---------|---------|
| G2023018 | 6.00/10 | 10.00/10 | 9.00/10 | 9.00/10 | 10.00/10 | 7.00/10 |
| alice | 8.50/10 | 7.00/10 | 8.00/10 | 9.50/10 | 9.00/10 | 8.00/10 |
| bob | 7.00/10 | 8.50/10 | 7.50/10 | 8.00/10 | 8.50/10 | 7.50/10 |

## 📋 說明

- **程式碼貢獻量**：提交次數、活躍度、產出量
- **技術廣度**：語言分布、檔案類型、技術棧
- **協作能力**：Merge Commits、衝突處理、Revert 率
- **Code Review 品質**：Review 參與度、深度、時效性
- **工作模式**：時間分布、穩定性
- **進步趨勢**：成長曲線、技能提升

---
**資料來源：** 各開發者的 analysis-result.md
```

### 執行流程

```bash
# Step 1: 掃描所有開發者目錄
find .\scripts\output\users\ -name "analysis-result.md" -type f

# Step 2: 讀取每個 analysis-result.md
#   - 提取開發者名稱
#   - 提取各維度評分

# Step 3: 產生彙整表格
#   - 使用 Markdown 表格格式
#   - 按照指定欄位順序排列

# Step 4: 儲存報告
#   輸出路徑：.\scripts\output\users\all-user-analysis-result.md
```

### 使用範例

```
使用者：彙整所有開發者的評估報告

Skill：好的！我將彙整所有開發者的評估報告。

[掃描評估報告]
Skill：🔍 正在掃描 .\scripts\output\users\ 目錄...
      找到 5 位開發者的評估報告：
      - G2023018
      - alice
      - bob
      - carol
      - david

[提取評估資料]
Skill：📊 正在提取各維度評分...

[產生彙整報告]
Skill：✅ 彙整報告已產生！

      📄 報告位置：.\scripts\output\users\all-user-analysis-result.md

      [顯示彙整表格摘要]

是否需要：
- 查看完整彙整報告？
- 依特定維度排序？
- 產生視覺化圖表？
```

### 注意事項

1. **資料來源**
   - 只彙整已存在的 `analysis-result.md` 檔案
   - 若某開發者尚未產生評估報告，將不會包含在彙整表中
   - 建議先執行各開發者的評估再進行彙整

2. **分數格式**
   - 保持原始分數格式（如 `6.00/10`）
   - 若某維度分數缺失，顯示為 `N/A`

3. **排序方式**
   - 預設按開發者 username 字母順序排列
   - 可選：按特定維度分數排序（如按「程式碼貢獻量」降冪排序）

---

## 限制與注意事項

1. **資料準確性**
   - 依賴 GitLab API 回傳資料
   - 可能受網路延遲影響
   - 歷史資料可能不完整（取決於 GitLab 設定）

2. **評估侷限**
   - 無法評估程式碼實際品質（需 code review）
   - 無法評估軟技能（溝通、領導力）
   - 量化指標不代表全部價值

3. **隱私考量**
   - 需取得開發者同意
   - 不應作為唯一績效評估依據
   - 報告應妥善保管，避免外洩

---

## 參考文檔

- 📚 **[gl-cli.py 完整操作手冊](../../../scripts/README.md)** - 工具使用指南、所有命令參數、範例與故障排除
- 📊 **[程式碼品質分析規範](./references/code-quality-analysis-spec.md)** - 詳細的評估維度、權重配置與評分標準

---

**使用此 Skill 時，請確保已設定好 GitLab 環境並擁有適當權限。**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yaochangyu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

---
name: ai-model-routing
description: >- Use when this capability is needed.
metadata:
  author: samchang72
---

# AI 模型智慧路由

你是一個 AI 模型協作專家，負責根據任務特性智慧選擇最適合的 AI 模型來完成工作。

## 核心原則

**模型分工策略：**
- **Claude Code CLI**：程式碼生成、修改、重構、除錯
- **Gemini (Antigravity)**：規劃、文件撰寫、研究分析、協調整合、瀏覽器操作、圖片生成

## 決策流程圖

```mermaid
flowchart TD
    START([收到任務]) --> TYPE{任務類型?}
    
    TYPE -->|程式碼修改| SAFE{安全層級?}
    TYPE -->|文件/規劃| GEMINI[使用 Gemini]
    TYPE -->|瀏覽器/圖片| GEMINI
    TYPE -->|混合型| HYBRID[先 Claude 寫 code<br/>再 Gemini 寫文件]
    
    SAFE -->|Level 1: 純分析| L1["claude -p 'query'<br/>--tools 'Read,Grep,Glob'"]
    SAFE -->|Level 2: 唯讀+Git| L2["claude -p 'query'<br/>--allowedTools 'Read,Grep,Glob,<br/>Bash(git diff *),Bash(git log *)'"]
    SAFE -->|Level 3: 讀寫修改| L3["claude -p 'query'<br/>--allowedTools 'Read,Edit,Write'<br/>--max-turns 10"]
    SAFE -->|Level 4: 完整權限| L4["claude -p 'query'<br/>--allowedTools 'Read,Edit,Write,Bash'<br/>--max-turns 20 --max-budget-usd 2"]
    
    L1 --> VERIFY[Gemini 驗證結果]
    L2 --> VERIFY
    L3 --> VERIFY
    L4 --> VERIFY
    
    VERIFY --> DONE([完成])
    GEMINI --> DONE
    HYBRID --> DONE
```

## Claude CLI 執行模式

| 模式 | 語法 | 用途 |
|------|------|------|
| **列印模式** | `claude -p "prompt"` | 自動化、腳本整合 |
| **管道模式** | `cat file \| claude -p "prompt"` | 傳入大量上下文 |
| **Session 串接** | `claude -p "prompt" --resume $session_id` | 多步驟任務 |

## 安全層級分類

### Level 1: 純分析（最安全）
```bash
# 僅允許讀取操作，無法修改任何檔案
claude -p "分析這個專案的架構" \
  --tools "Read,Grep,Glob" \
  --output-format json
```

### Level 2: 唯讀 + 受控 Git
```bash
# 可執行特定 Git 命令（只讀）
claude -p "審查最近的 commit" \
  --allowedTools "Read,Grep,Glob,Bash(git diff *),Bash(git log *),Bash(git status *)"
```

### Level 3: 讀寫修改
```bash
# 可編輯檔案，但不能執行任意 Bash
claude -p "修復所有 lint 錯誤" \
  --allowedTools "Read,Edit,Write" \
  --max-turns 10
```

### Level 4: 完整權限（需謹慎）
```bash
# 完整權限，需設定成本上限
claude -p "重構整個模組並執行測試" \
  --allowedTools "Read,Edit,Write,Bash" \
  --max-turns 20 \
  --max-budget-usd 2.00 \
  --fallback-model haiku
```

## 成本控制機制

```bash
# 設定預算上限（美元）
claude -p "大型任務" --max-budget-usd 5.00

# 限制回合數（避免無限迴圈）
claude -p "任務" --max-turns 10

# 過載時自動回退到輕量模型
claude -p "任務" --fallback-model haiku

# 組合使用
claude -p "複雜任務" \
  --max-budget-usd 2.00 \
  --max-turns 15 \
  --fallback-model haiku
```

## 結構化輸出

### JSON 輸出格式
```bash
# 取得結構化回應（含 session_id 和 token 使用量）
claude -p "分析專案" --output-format json

# 回傳結構：
# {
#   "result": "...",
#   "session_id": "abc123",
#   "usage": { "input_tokens": 150, "output_tokens": 200 }
# }
```

### 強制結構化輸出
```bash
# 使用 JSON Schema 確保輸出格式
claude -p "提取所有 API endpoint" \
  --output-format json \
  --json-schema '{
    "type": "object",
    "properties": {
      "endpoints": {
        "type": "array",
        "items": {
          "type": "object",
          "properties": {
            "method": {"type": "string"},
            "path": {"type": "string"}
          }
        }
      }
    }
  }'
```

## Session 串接

```bash
# 第一步：執行任務並取得 session_id
session_id=$(claude -p "分析 auth.py 的安全問題" \
  --output-format json | jq -r '.session_id')

# 第二步：延續對話
claude -p "針對剛才發現的問題，提供修復方案" \
  --resume "$session_id"

# 第三步：實際修復
claude -p "執行修復" \
  --resume "$session_id" \
  --allowedTools "Read,Edit,Write"
```

## 管道模式

```bash
# 傳入 Git diff 進行審查
git diff main | claude -p "審查這些變更，關注安全性" \
  --output-format json

# 傳入錯誤日誌進行分析
cat error.log | claude -p "解釋這個錯誤並提供修復建議"

# 傳入 build 輸出
npm run build 2>&1 | claude -p "摘要失敗原因"
```

## 系統提示自訂

```bash
# 附加提示（推薦：保留預設能力）
claude -p "任務" \
  --append-system-prompt "使用 TypeScript，加上完整 JSDoc 註解"

# 從檔案載入（可重複使用）
claude -p "任務" \
  --append-system-prompt-file ./project-rules.txt

# 完全取代系統提示（謹慎使用）
claude -p "任務" \
  --system-prompt "你是 Python 安全專家"
```

## 兩階段執行模式

```bash
# 階段一：規劃（唯讀）
claude -p "分析並規劃如何實作快取機制，只輸出計畫" \
  --allowedTools "Read,Grep,Glob" \
  --output-format json | jq -r '.result' > plan.txt

# 人工審核 plan.txt ...

# 階段二：執行（讀寫）
claude -p "依照以下計畫實作：$(cat plan.txt)" \
  --allowedTools "Read,Edit,Write,Bash" \
  --max-turns 20
```

## 模型特性比較

| 特性 | Claude Code CLI | Gemini (Antigravity) |
|------|-----------------|---------------------|
| 程式碼生成 | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| 程式碼修改 | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| 規劃設計 | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| 文件撰寫 | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| 多工協調 | ⭐⭐ | ⭐⭐⭐⭐⭐ |
| 瀏覽器操作 | ❌ | ⭐⭐⭐⭐⭐ |
| 圖片生成 | ❌ | ⭐⭐⭐⭐⭐ |

## 錯誤處理

```bash
# 檢查 CLI 是否可用
which claude && claude --version

# 如果 Claude CLI 不可用，由 Gemini 直接處理
# （效率較低但可作為備援）
```

## 快速參考

```
需要寫程式？ → claude -p "..." (優先使用)
需要寫文件？ → Gemini 直接處理
需要瀏覽網頁？ → 只能用 Gemini
需要生成圖片？ → 只能用 Gemini
多步驟任務？ → Session 串接
安全審查？ → Level 1-2 (唯讀)
自動修復？ → Level 3-4 (讀寫)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/samchang72) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

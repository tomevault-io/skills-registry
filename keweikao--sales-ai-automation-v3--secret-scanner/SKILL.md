---
name: secret-scanner
description: 自動掃描程式碼中的敏感資訊。當準備 commit、編輯設定檔、新增環境變數、或建立 PR 時自動執行。檢測 API keys、tokens、密碼、資料庫連線字串等敏感資訊，防止意外洩漏。 Use when this capability is needed.
metadata:
  author: keweikao
---

# Secret Scanner - 敏感資訊掃描

## 自動觸發時機

Claude 會在以下情況**自動執行**此 skill：

| 觸發情境 | 說明 |
|---------|------|
| 準備 Commit | 在 commit 前檢查 staged 檔案 |
| 編輯設定檔 | 修改 `.env`、`config` 等檔案 |
| 新增環境變數 | 任何涉及環境變數的變更 |
| 建立 PR | PR 建立前的最終檢查 |
| 新增檔案 | 建立新檔案時檢查內容 |

## 掃描模式

### 1. API Keys & Tokens

```regex
# AWS
AKIA[0-9A-Z]{16}
aws[_-]?(secret[_-]?access[_-]?key|access[_-]?key[_-]?id)

# Anthropic
sk-ant-[a-zA-Z0-9-_]{40,}

# OpenAI
sk-[a-zA-Z0-9]{48}

# Gemini/Google
AIza[0-9A-Za-z-_]{35}

# Groq
gsk_[a-zA-Z0-9]{52}

# Slack
xox[baprs]-[0-9]{10,13}-[a-zA-Z0-9-]+

# GitHub
gh[pousr]_[A-Za-z0-9_]{36,}
github_pat_[a-zA-Z0-9]{22}_[a-zA-Z0-9]{59}

# Generic
api[_-]?key\s*[:=]\s*['"][^'"]{20,}['"]
secret[_-]?key\s*[:=]\s*['"][^'"]{20,}['"]
```

### 2. 資料庫連線

```regex
# PostgreSQL
postgres(ql)?://[^:]+:[^@]+@[^/]+/\w+

# MySQL
mysql://[^:]+:[^@]+@[^/]+/\w+

# MongoDB
mongodb(\+srv)?://[^:]+:[^@]+@[^/]+

# Redis
redis://[^:]+:[^@]+@[^:]+:\d+
```

### 3. 私鑰與憑證

```regex
# Private Keys
-----BEGIN (RSA |EC |OPENSSH |DSA )?PRIVATE KEY-----
-----BEGIN PGP PRIVATE KEY BLOCK-----

# Certificates
-----BEGIN CERTIFICATE-----
```

### 4. 密碼模式

```regex
password\s*[:=]\s*['"][^'"]+['"]
passwd\s*[:=]\s*['"][^'"]+['"]
pwd\s*[:=]\s*['"][^'"]+['"]
```

## 執行流程

### 步驟 1: 識別掃描範圍

```bash
# 查看 staged 檔案
git diff --cached --name-only

# 或掃描特定目錄
find . -type f \( -name "*.ts" -o -name "*.js" -o -name "*.env*" -o -name "*.json" \)
```

### 步驟 2: 排除安全檔案

**白名單（不掃描）：**
- `.env.example` - 範例檔案（無真實值）
- `*.test.ts` - 測試檔案中的 mock 值
- `node_modules/` - 第三方套件
- `.git/` - Git 內部檔案

### 步驟 3: 執行掃描

對每個檔案：
1. 讀取內容
2. 執行所有敏感模式匹配
3. 記錄發現的問題

### 步驟 4: 驗證發現

區分真正的敏感資訊和誤報：

| 類型 | 處理方式 |
|------|---------|
| 真實 API Key | 🔴 立即警告，阻止提交 |
| 環境變數引用 | ✅ 安全（如 `process.env.API_KEY`）|
| 範例/Mock 值 | ✅ 安全（如 `sk-test-xxx`）|
| 文件說明 | ✅ 安全（文檔中的格式說明）|

## 輸出格式

### 發現敏感資訊

```markdown
## 🚨 Secret Scanner 警告

### 發現敏感資訊！

| 檔案 | 行號 | 類型 | 風險等級 |
|------|------|------|---------|
| `apps/server/config.ts` | 15 | API Key | 🔴 高 |
| `packages/env/index.ts` | 42 | DB Password | 🔴 高 |

### 詳細資訊

#### 🔴 apps/server/config.ts:15
**類型**: Gemini API Key
**發現內容**: `AIzaSyB...`（已遮蔽）
**建議**: 移至環境變數

\`\`\`typescript
// ❌ 不安全
const apiKey = "AIzaSyB...";

// ✅ 安全
const apiKey = process.env.GEMINI_API_KEY;
\`\`\`

### ⛔ 行動要求
1. **不要提交這些變更**
2. 將敏感資訊移至 `.env` 檔案
3. 確保 `.env` 在 `.gitignore` 中
4. 重新掃描確認安全
```

### 掃描通過

```markdown
## ✅ Secret Scanner 通過

**掃描範圍**: X 個檔案
**掃描時間**: YYYY-MM-DD HH:mm

### 檢查項目
- [x] API Keys & Tokens
- [x] 資料庫連線字串
- [x] 私鑰與憑證
- [x] 硬編碼密碼

### 白名單排除
- `.env.example` (範例檔案)
- `tests/**` (測試 mock)

**結論**: 未發現敏感資訊，可以安全提交。
```

## 專案特定規則

### 此專案的敏感資訊位置

| 環境變數 | 用途 | 應在檔案 |
|---------|------|---------|
| `GEMINI_API_KEY` | Gemini AI | `.env` |
| `GROQ_API_KEY` | Groq Whisper | `.env` |
| `SLACK_BOT_TOKEN` | Slack Bot | Cloudflare Secrets |
| `DATABASE_URL` | PostgreSQL | `.env` / Cloudflare |
| `BETTER_AUTH_SECRET` | Auth 加密 | `.env` |

### 安全的引用方式

```typescript
// ✅ 正確：從環境變數讀取
import { env } from "@sales-ai/env";
const apiKey = env.GEMINI_API_KEY;

// ❌ 錯誤：硬編碼
const apiKey = "AIzaSyB...";
```

## 整合的工具

| 工具 | 用途 |
|------|------|
| `Grep` | 執行正則匹配 |
| `Read` | 讀取檔案內容 |
| `Glob` | 找出需掃描的檔案 |
| `Bash(git)` | 識別變更範圍 |

## 相關 Skills

- `/commit` - Commit 前自動執行掃描
- `/pr-review` - PR 前執行掃描
- `/security-audit` - 完整安全審計

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/keweikao) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

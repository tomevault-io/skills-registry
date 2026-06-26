---
name: claude-code-guide
description: Claude Code 高階開發指南 - 全面的中文教程，涵蓋工具使用、REPL 環境、開發工作流、MCP 整合、高階模式和最佳實踐。適合學習 Claude Code 的高階功能和開發技巧。 Use when this capability is needed.
metadata:
  author: godmakereth
---

# Claude Code 高階開發指南

全面的 Claude Code 中文學習指南，涵蓋從基礎到高階的所有核心概念、工具使用、開發工作流和最佳實踐。

## 何時使用此技能

當需要以下幫助時使用此技能：
- 學習 Claude Code 的核心功能和工具
- 掌握 REPL 環境的高階用法
- 理解開發工作流和任務管理
- 使用 MCP 整合外部系統
- 實現高階開發模式
- 應用 Claude Code 最佳實踐
- 解決常見問題和錯誤
- 進行大檔案分析和處理

## 快速參考

### Claude Code 核心工具（7個）

1. **REPL** - JavaScript 執行時環境
   - 完整的 ES6+ 支援
   - 預載入庫：D3.js, MathJS, Lodash, Papaparse, SheetJS
   - 支援 async/await, BigInt, WebAssembly
   - 檔案讀取：`window.fs.readFile()`

2. **Artifacts** - 視覺化輸出
   - React, Three.js, 圖表庫
   - HTML/SVG 渲染
   - 互動式元件

3. **Web Search** - 網路搜尋
   - 僅美國可用
   - 域名過濾支援

4. **Web Fetch** - 獲取網頁內容
   - HTML 轉 Markdown
   - 內容提取和分析

5. **Conversation Search** - 對話搜尋
   - 搜尋歷史對話
   - 上下文檢索

6. **Recent Chats** - 最近對話
   - 訪問最近會話
   - 對話歷史

7. **End Conversation** - 結束對話
   - 清理和總結
   - 會話管理

### 大檔案分析工作流

```bash
# 階段 1：定量評估
wc -l filename.md    # 行數統計
wc -w filename.md    # 詞數統計
wc -c filename.md    # 字元數統計

# 階段 2：結構分析
grep "^#{1,6} " filename.md  # 提取標題層次
grep "```" filename.md       # 識別程式碼塊
grep -c "keyword" filename.md # 關鍵詞頻率

# 階段 3：內容提取
Read filename.md offset=0 limit=50      # 檔案開頭
Read filename.md offset=N limit=100     # 目標部分
Read filename.md offset=-50 limit=50    # 檔案結尾
```

### REPL 高階用法

```javascript
// 資料處理
const data = [1, 2, 3, 4, 5];
const sum = data.reduce((a, b) => a + b, 0);

// 使用預載入庫
// Lodash
_.chunk([1, 2, 3, 4], 2);  // [[1,2], [3,4]]

// MathJS
math.sqrt(16);  // 4

// D3.js
d3.range(10);  // [0,1,2,3,4,5,6,7,8,9]

// 讀取檔案
const content = await window.fs.readFile('path/to/file');

// 非同步操作
const result = await fetch('https://api.example.com/data');
const json = await result.json();
```

### 斜槓命令系統

**內建命令：**
- `/help` - 顯示幫助
- `/clear` - 清除對話
- `/plugin` - 管理外掛
- `/settings` - 配置設定

**自定義命令：**
建立 `.claude/commands/mycommand.md`：
```markdown
根據需求執行特定任務的指令
```

使用：`/mycommand`

### 開發工作流模式

#### 1. 檔案分析工作流
```bash
# 探索 → 理解 → 實現
ls -la                  # 列出檔案
Read file.py            # 讀取內容
grep "function" file.py # 搜尋模式
# 然後實現修改
```

#### 2. 演算法驗證工作流
```bash
# 設計 → 驗證 → 實現
# 1. 在 REPL 中測試邏輯
# 2. 驗證邊界情況
# 3. 實現到程式碼
```

#### 3. 資料探索工作流
```bash
# 檢查 → 分析 → 視覺化
# 1. 讀取資料檔案
# 2. REPL 中分析
# 3. Artifacts 視覺化
```

## 核心概念

### 工具許可權系統

**自動授予許可權的工具：**
- REPL
- Artifacts  
- Web Search/Fetch
- Conversation Search

**需要授權的工具：**
- Bash (讀/寫檔案系統)
- Edit (修改檔案)
- Write (建立檔案)

### 專案上下文

Claude 自動識別：
- Git 倉庫狀態
- 程式語言（從副檔名）
- 專案結構
- 依賴配置

### 記憶體系統

**對話記憶體：**
- 儲存在當前會話
- 200K token 視窗
- 自動上下文管理

**持久記憶體（實驗性）：**
- 跨會話儲存
- 使用者偏好記憶
- 專案上下文保留

## MCP 整合

### 什麼是 MCP？

Model Context Protocol - 連線 Claude 到外部系統的協議。

### MCP 伺服器配置

配置檔案：`~/.config/claude/mcp_config.json`

```json
{
  "mcpServers": {
    "my-server": {
      "command": "node",
      "args": ["path/to/server.js"],
      "env": {
        "API_KEY": "your-key"
      }
    }
  }
}
```

### 使用 MCP 工具

Claude 會自動發現 MCP 工具並在對話中使用：

```
"使用 my-server 工具獲取資料"
```

## 鉤子系統

### 鉤子型別

在 `.claude/settings.json` 配置：

```json
{
  "hooks": {
    "tool-pre-use": "echo 'About to use tool'",
    "tool-post-use": "echo 'Tool used'",
    "user-prompt-submit": "echo 'Processing prompt'"
  }
}
```

### 常見鉤子用途

- 自動格式化程式碼
- 執行測試
- Git 提交檢查
- 日誌記錄
- 通知傳送

## 高階模式

### 多代理協作

使用 Task 工具啟動子代理：

```
"啟動一個專門的代理來最佳化這個演算法"
```

子代理特點：
- 獨立上下文
- 專注單一任務
- 返回結果到主代理

### 智慧任務管理

使用 TodoWrite 工具：

```
"建立任務列表來跟蹤這個專案"
```

任務狀態：
- `pending` - 待處理
- `in_progress` - 進行中  
- `completed` - 已完成

### 程式碼生成模式

**漸進式開發：**
1. 生成基礎結構
2. 新增核心功能
3. 實現細節
4. 測試和最佳化

**驗證驅動：**
1. 寫測試用例
2. 實現功能
3. 執行測試
4. 修復問題

## 質量保證

### 自動化測試

```bash
# 執行測試
npm test
pytest

# 型別檢查
mypy script.py
tsc --noEmit

# 程式碼檢查
eslint src/
flake8 .
```

### 程式碼審查模式

使用子代理進行審查：

```
"啟動程式碼審查代理檢查這個檔案"
```

審查重點：
- 程式碼質量
- 安全問題
- 效能最佳化
- 最佳實踐

## 錯誤恢復

### 常見錯誤模式

1. **工具使用錯誤**
   - 檢查許可權
   - 驗證語法
   - 確認路徑

2. **檔案操作錯誤**
   - 確認檔案存在
   - 檢查讀寫許可權
   - 驗證路徑正確

3. **API 呼叫錯誤**
   - 檢查網路連線
   - 驗證 API 金鑰
   - 確認請求格式

### 漸進式修復策略

1. 隔離問題
2. 最小化復現
3. 逐步修復
4. 驗證解決方案

## 最佳實踐

### 開發原則

1. **清晰優先** - 明確需求和目標
2. **漸進實現** - 分步驟開發
3. **持續驗證** - 頻繁測試
4. **適當抽象** - 合理模組化

### 工具使用原則

1. **正確的工具** - 選擇合適的工具
2. **工具組合** - 多工具協同
3. **許可權最小化** - 只請求必要許可權
4. **錯誤處理** - 優雅處理失敗

### 效能最佳化

1. **批次操作** - 合併多個操作
2. **增量處理** - 處理大檔案
3. **快取結果** - 避免重複計算
4. **非同步優先** - 使用 async/await

## 安全考慮

### 沙箱模型

每個工具在隔離環境中執行：
- REPL：無檔案系統訪問
- Bash：需要明確授權
- Web：僅特定域名

### 最佳安全實踐

1. **最小許可權** - 僅授予必要許可權
2. **程式碼審查** - 檢查生成的程式碼
3. **敏感資料** - 不要共享金鑰
4. **定期審計** - 檢查鉤子和配置

## 故障排除

### 工具無法使用

**症狀：** 工具呼叫失敗

**解決方案：**
- 檢查許可權設定
- 驗證語法正確
- 確認檔案路徑
- 檢視錯誤訊息

### REPL 效能問題

**症狀：** REPL 執行緩慢

**解決方案：**
- 減少資料量
- 使用流式處理
- 最佳化演算法
- 分批處理

### MCP 連線失敗

**症狀：** MCP 伺服器無響應

**解決方案：**
- 檢查配置檔案
- 驗證伺服器執行
- 確認環境變數
- 檢視伺服器日誌

## 實用示例

### 示例 1：資料分析

```javascript
// 在 REPL 中
const data = await window.fs.readFile('data.csv');
const parsed = Papa.parse(data, { header: true });
const values = parsed.data.map(row => parseFloat(row.value));
const avg = _.mean(values);
const std = math.std(values);
console.log(`平均值: ${avg}, 標準差: ${std}`);
```

### 示例 2：檔案搜尋

```bash
# 在 Bash 中
grep -r "TODO" src/
find . -name "*.py" -type f
```

### 示例 3：網路資料獲取

```
"使用 web_fetch 獲取 https://api.example.com/data 的內容，
然後在 REPL 中分析 JSON 資料"
```

## 參考檔案

此技能包含詳細文件：

- **README.md** (9,594 行) - 完整的 Claude Code 高階指南

包含以下主題：
- 核心工具深度解析
- REPL 高階協同模式
- 開發工作流詳解
- MCP 整合完整指南
- 鉤子系統配置
- 高階模式和最佳實踐
- 故障排除和安全考慮

使用 `view` 命令檢視參考檔案獲取詳細資訊。

## 資源

- **GitHub 倉庫**: https://github.com/karminski/claude-code-guide-study
- **原始版本**: https://github.com/Cranot/claude-code-guide
- **Anthropic 官方文件**: https://docs.claude.com

## 注意事項

本指南結合了：
- 官方功能和公告
- 實際使用觀察到的模式
- 概念性方法和最佳實踐
- 第三方工具整合

請在使用時參考最新的官方文件。

---

**使用這個技能深入掌握 Claude Code 的強大功能！**

---
> Source: [godmakereth/vibe-coding-tw](https://github.com/godmakereth/vibe-coding-tw) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-25 -->

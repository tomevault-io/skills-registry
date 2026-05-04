---
name: code-reviewer
description: 程式碼審查。觸發：review、審查、檢查、看一下、有沒有問題、安全。 Use when this capability is needed.
metadata:
  author: neversight
---

# 程式碼審查技能

## 觸發條件

| 用戶說法 | 觸發 |
|----------|------|
| review、審查程式碼 | ✅ |
| 檢查、看一下 | ✅ |
| 有沒有問題、安全 | ✅ |

---

## 可用工具

| 操作 | 工具 |
|------|------|
| 讀取程式碼 | `read_file()` |
| 搜尋模式 | `grep_search()` |
| 取得錯誤 | `get_errors()` |
| 執行 linter | `run_in_terminal("ruff check .")` |
| 執行型別檢查 | `run_in_terminal("mypy .")` |

---

## 審查項目

### 1. 程式碼品質

| 項目 | 檢查方式 |
|------|----------|
| 函數長度 | `grep_search("def ")` + 計算行數 |
| 命名清晰度 | 人工審查 |
| DRY 原則 | 搜尋重複模式 |
| 複雜度 | 巢狀層級、條件分支數 |

### 2. 安全性

| 風險 | 檢查方式 |
|------|----------|
| SQL 注入 | `grep_search("execute.*%s|f\".*SELECT")` |
| XSS | `grep_search("innerHTML|dangerouslySetInnerHTML")` |
| 敏感資料 | `grep_search("password|secret|api_key")` |
| 硬編碼密碼 | `grep_search("password.*=.*['\"]")` |

### 3. 效能

| 問題 | 檢查方式 |
|------|----------|
| N+1 查詢 | 審查迴圈內的資料庫呼叫 |
| 不必要迴圈 | 審查可用 list comprehension 的地方 |

---

## 標準工作流程

```python
# 1. 執行靜態分析
run_in_terminal("ruff check src/")
run_in_terminal("mypy src/")

# 2. 取得編輯器錯誤
get_errors()

# 3. 讀取目標檔案
read_file("src/target_file.py")

# 4. 搜尋安全風險模式
grep_search(query="execute.*%s", isRegexp=True)
grep_search(query="password.*=.*['\"]", isRegexp=True)

# 5. 彙整報告
```

---

## 輸出格式

```
## 🔍 審查結果：src/auth/login.py

### ✅ 優點
- 函數切分合理
- 錯誤處理完整

### ⚠️ 建議改進

**[高] SQL 注入風險**
- 位置：第 45 行
- 問題：使用字串格式化建構 SQL
- 建議：使用參數化查詢

**[中] 函數過長**
- 位置：第 20-85 行 `process_login()`
- 問題：65 行超過建議的 50 行
- 建議：拆分為多個子函數

### 📊 評分
| 項目 | 分數 |
|------|------|
| 品質 | 7/10 |
| 安全 | 5/10 |
| 效能 | 8/10 |
```

---

## 相關技能

- `ddd-architect` - 架構層面審查
- `test-generator` - 生成測試覆蓋
- `code-refactor` - 執行重構建議

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

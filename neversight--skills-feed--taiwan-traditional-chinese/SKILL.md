---
name: taiwan-traditional-chinese
description: READ FIRST before ANY Traditional Chinese output (files, docs, markdown, comments, translations). Taiwan zh_TW terminology standards. Must read when creating content, writing documentation, or responding in Chinese. Use when this capability is needed.
metadata:
  author: neversight
---

# Taiwan Traditional Chinese Response Skill

台灣繁體中文回應指南。

## 🔴 MANDATORY PRE-CHECK

**Before generating ANY Traditional Chinese content, you MUST:**

1. ✅ Read `references/guidelines.md` for complete guidelines
2. ✅ Check terminology against Taiwan conventions
3. ✅ Verify technical terms stay in English

**Common mistakes when NOT reading this skill:**

- ❌ 代碼 → ✅ 程式碼
- ❌ 数据 → ✅ 資料
- ❌ 组件 → ✅ 元件
- ❌ 应用程序 → ✅ 應用程式
- ❌ 数据库 → ✅ 資料庫
- ❌ 服务器 → ✅ 伺服器

---

## Quick Reference

### Core Principles

| 原則 | 說明 | 例子 |
|------|------|------|
| **字體** | 繁體中文（zh_TW），非簡體 | ✓ 資料 ✗ 数据 |
| **術語** | 台灣慣例 | ✓ 應用程式 ✗ 应用程序 |
| **英文** | 保留框架和程式碼 | ✓ React state ✗ 瑞克特狀態 |
| **標點** | 句子全形、程式碼半形 | ✓ 在 `useState()` 中。 ✗ 在 useState（）中。|
| **語氣** | 專業且親切 | ✓ 我建議 ✗ 茲建議閣下 |

---

## When to Use

- 使用者以台灣繁體中文提問或要求中文輸出
- 撰寫或審查文件、註解、提交訊息
- 介面文案、在地化內容、翻譯
- 需要統一技術術語、標點與語氣風格

---

## Steps

1. **Read guidelines**: 先閱讀 [`references/guidelines.md`](./references/guidelines.md) 了解完整規範
2. **Check terms**: 使用 [`references/terms.csv`](./references/terms.csv) 查詢不確定的術語
3. **Apply rules**: 
   - 術語用台灣慣例（資料、元件、伺服器）
   - 框架名稱保留英文（React、useState、API）
   - 句子全形標點、程式碼半形標點
   - 程式碼與檔名加反引號
4. **Verify**: 使用下方 Checklist 檢查

---

## Essential Examples

### ✓ Correct

```javascript
// 程式碼保持英文，註解用繁中
import { useState, useEffect } from 'react'

// 初始化使用者狀態
const [user, setUser] = useState(null)

// 呼叫 API 取得資料
useEffect(() => {
  fetch('/api/users')
}, [])
```

```markdown
使用 React 框架，在 `useEffect()` 中處理資料載入。
檔案位於 `src/components/Button.tsx`。
```

```bash
# Commit 訊息
feat(member): 新增使用者編輯功能
fix: 修正 component 重複 render 的問題
```

### ✗ Common Mistakes

```javascript
// ✗ 過度翻譯
const [使用者, 設置使用者] = useState(null)

// ✗ Mainland 術語
// 初始化用户状态

// ✗ 標點混亂
使用 `useState` hook。但要注意 dependency
```

---

## Quality Checklist

- [ ] 使用繁體中文（zh_TW），非簡體
- [ ] 術語符合台灣慣例（資料、應用程式、伺服器）
- [ ] 英文專有名詞保留（React、useState、API）
- [ ] 句子用全形標點、程式碼用半形標點
- [ ] 程式碼與檔名加反引號

---

## References

### 必讀文件
- 📄 [guidelines.md](./references/guidelines.md) - 完整指南（術語、標點、格式、範例）
- 📁 [terms.csv](./references/terms.csv) - 460+ 術語對照表

### 外部資源
- [Wikibooks 對照表](https://zh.wikibooks.org/zh-tw/%E5%A4%A7%E9%99%86%E5%8F%B0%E6%B9%BE%E8%AE%A1%E7%AE%97%E6%9C%BA%E6%9C%AF%E8%AF%AD%E5%AF%B9%E7%85%A7%E8%A1%A8) - CC BY-SA 4.0
- [教育部重編國語辭典](https://dict.revised.moe.edu.tw/)

---

**Last Updated**: 2026-01-23

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

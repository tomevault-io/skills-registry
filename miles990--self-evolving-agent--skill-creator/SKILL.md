---
name: skill-creator
description: 引導式建立新 Skill 的完整工作流 Use when this capability is needed.
metadata:
  author: miles990
---

# Skill Creator

> 完整工作流：引導式訪談 → 分析生成 → 驗證 → **Token 優化** → 發布到 GitHub

## 使用方式

```bash
/evolve --new-skill "skill 名稱"
```

## 五階段流程

### Stage 1: 引導式訪談

向使用者提問，收集需求：

1. **問題定義**：這個 skill 要解決什麼問題？
2. **目標使用者**：新手 / 進階 / 專家？
3. **前置需求**：需要什麼 MCP servers 或 CLI tools？
4. **參考來源**：有沒有類似的 skill 可以參考？

輸出：內部需求文件

### Stage 2: 分析 + 生成

**搜尋順序**：

1. **優先搜尋官方 Skill Repos**：
   - [claude-domain-skills](https://github.com/miles990/claude-domain-skills) — 領域知識 skills
   - [claude-software-skills](https://github.com/miles990/claude-software-skills) — 軟體開發 skills
2. 如無適合，搜尋 GitHub 上其他 skills
3. **如都無適合參考，使用 4C 方法自行研究**：
   - 載入 `methodology/knowledge-acquisition-4c` skill（from claude-domain-skills）
   - **Collect**：WebSearch / WebFetch 收集官方文檔、最佳實踐
   - **Curate**：篩選高品質來源，去除噪音
   - **Contextualize**：分析領域核心概念和常見流程
   - **Codify**：整理成 skill 可用的知識結構

**生成流程**：

4. 選擇適合的範本（basic / advanced）
5. 生成 SKILL.md 初稿
6. 建立目錄結構（如需要 scripts/templates）

輸出：完整的 skill 目錄

### Stage 3: 驗證

檢查清單：
- [ ] SKILL.md frontmatter 格式正確
- [ ] 必要欄位存在（name, description, version）
- [ ] 模擬使用情境，確認指令清楚
- [ ] 如有 scripts，確認可執行

輸出：驗證報告

### Stage 3.5: Token 優化

使用 `skill-optimizer` 優化新建立的 skill：

1. **分析 token 效率**：
   - 檢查總行數（目標 < 300 行）
   - 計算核心內容佔比（目標 > 70%）
   - 識別可外連的內容（大型範例、ASCII 圖表、模板）

2. **執行優化**：
   - 大型 ASCII 圖表 → 簡化為單行描述
   - 完整範例（> 20 行）→ 外連至 `extended/examples.md`
   - 模板（> 10 行）→ 外連至 `extended/templates.md`
   - 配置範例 → 外連至擴展檔案

3. **建立分層結構**（如需要）：
   ```
   skill-name/
   ├── SKILL.md           # 核心層 (< 300 行)
   └── extended/          # 擴展層 (按需載入)
       ├── examples.md
       └── templates.md
   ```

4. **驗證優化結果**：
   - 優化前後行數比較
   - 確認功能完整性未受影響

輸出：優化報告（節省 X% tokens）

> 💡 參考：[claude-domain-skills/methodology/skill-optimizer](https://github.com/miles990/claude-domain-skills/tree/main/methodology/skill-optimizer)

### Stage 4: 發布到 GitHub

1. 詢問：建立新 repo 或加入現有 repo？
2. 生成 README.md
3. git init + commit + push
4. 輸出安裝指令

輸出：
```
✅ Skill 已發布！

GitHub: https://github.com/<user>/<repo>
安裝: /plugin install <user>/<repo>
```

## 範本選擇指南

| 情況 | 範本 |
|------|------|
| 簡單指令、無依賴 | basic-skill.md |
| 需要 MCP、有複雜流程 | advanced-skill.md |

## 驗證腳本

```bash
./scripts/validate-skill.sh <skill-directory>
```

## 發布腳本

```bash
./scripts/publish-skill.sh <skill-directory> [--new-repo]
```

## Plugin 格式轉換

將 Skills 倉庫轉換為 Claude Code Plugin Marketplace 格式：

```bash
./scripts/convert-to-plugin.sh <skills-repo-path> [--marketplace|--category]
```

**模式：**
- `--marketplace`：建立 marketplace.json，頂層目錄成為 plugin（推薦）
- `--category`：為每個頂層分類建立獨立的 plugin.json

**範例：**
```bash
# 轉換整個 Skills 倉庫為 marketplace
./scripts/convert-to-plugin.sh ~/Workspace/my-skills --marketplace

# 輸出：
# ✅ marketplace.json 已建立
# ✅ 各分類 plugin.json 已建立
#
# 安裝指令:
#   /plugin marketplace add <user>/my-skills
#   /plugin install <plugin-name>@my-skills
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/miles990) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

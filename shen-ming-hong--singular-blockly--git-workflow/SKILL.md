---
name: git-workflow
description: Git 工作流程自動化技能，涵蓋從 commit 到發布的完整流程。當使用者提到 commit、push、建立 PR、pull request、提交程式碼、推送分支時自動啟用。包含自動生成 Conventional Commits 格式訊息、建立 PR、等待 Code Review、觸發發布流程等功能。Automates Git workflow from commit to release. Inspired by Anthropic's official commit-commands plugin. Use when this capability is needed.
metadata:
  author: shen-ming-hong
---

# Git 工作流程技能 Git Workflow Skill

自動化開發過程中的 Git 操作，從 commit 到建立 PR 的完整流程。
Automates Git operations during development, from commit to PR creation.

## 核心原則 Core Principles

> **端到端整合**：此技能現在涵蓋從「開發完成」到「版本發布」的完整流程。PR 建立後自動觸發 `pr-review-release` 技能，確保流程不中斷。
> **End-to-End Integration**: This skill now covers the complete flow from "development complete" to "version release". After PR creation, it automatically triggers `pr-review-release` skill to ensure uninterrupted workflow.

## 適用情境 When to Use

- 完成功能開發，需要提交程式碼
- 準備建立 Pull Request
- 在 spec 分支（如 `016-esp32-wifi-mqtt`）工作時

## 與其他技能的分工 Skill Boundaries

| 階段             | 技能                                   | 說明                             |
| ---------------- | -------------------------------------- | -------------------------------- |
| 開發中 → PR 建立 | **git-workflow** (本技能)              | commit, push, 建立 PR            |
| PR 建立 → 發布   | **git-workflow** + `pr-review-release` | 本技能強制觸發 pr-review-release |
| 程式碼簡化       | `code-simplifier`                      | PR 前必須執行（阻塞型）          |

---

## 工作流程 Workflow

### Phase 1: 自動 Commit Auto Commit

根據變更內容自動生成符合 Conventional Commits 格式的 commit message。

#### 1.1 分析變更

```bash
# 查看所有變更（staged + unstaged）
git status

# 查看詳細差異
git diff
git diff --cached  # 已 staged 的變更
```

#### 1.2 生成 Commit Message

**Conventional Commits 格式**：

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

**Type 類型**：

| Type       | 說明                           | 範例                                            |
| ---------- | ------------------------------ | ----------------------------------------------- |
| `feat`     | 新功能                         | `feat(wifi): add ESP32 WiFi connection blocks`  |
| `fix`      | Bug 修復                       | `fix(text): correct text_join type conversion`  |
| `docs`     | 文件更新                       | `docs(i18n): update Chinese translations`       |
| `style`    | 格式調整（不影響程式碼邏輯）   | `style: format with prettier`                   |
| `refactor` | 重構（不新增功能也不修復 bug） | `refactor(generator): simplify code generation` |
| `test`     | 測試相關                       | `test(fileService): add unit tests`             |
| `chore`    | 建置/工具/依賴更新             | `chore(deps): upgrade blockly to 12.3.1`        |

**Scope 範圍**（專案特定）：

| Scope        | 說明                                       |
| ------------ | ------------------------------------------ |
| `blocks`     | 積木定義 (`media/blockly/blocks/`)         |
| `generators` | 程式碼生成器 (`media/blockly/generators/`) |
| `i18n`       | 國際化 (`media/locales/`)                  |
| `webview`    | WebView 相關 (`media/js/`, `src/webview/`) |
| `mcp`        | MCP Server (`src/mcp/`)                    |
| `services`   | 服務層 (`src/services/`)                   |
| `toolbox`    | 工具箱 (`media/toolbox/`)                  |
| `deps`       | 依賴管理                                   |

#### 1.3 執行 Commit

```bash
# Stage 變更
git add .
# 或選擇性 stage
git add -p

# Commit
git commit -m "feat(scope): description"
```

#### 1.4 多次 Commit 策略

對於大型功能，建議分多次 commit：

```bash
# 範例：ESP32 WiFi/MQTT 功能
git commit -m "feat(blocks): add WiFi block definitions"
git commit -m "feat(generators): implement WiFi code generators"
git commit -m "feat(i18n): add WiFi block translations (15 languages)"
git commit -m "docs(toolbox): add WiFi blocks to communication category"
```

---

### Phase 2: 推送分支 Push Branch

```bash
# 推送到遠端（首次推送功能分支）
git push -u origin HEAD

# 後續推送
git push
```

**分支命名規範**（SDD 整合）：

- Spec 分支：`{NNN}-feature-name`（如 `016-esp32-wifi-mqtt`）
- 修復分支：`fix/{issue-number}-description`
- 文件分支：`docs/{description}`

---

### Phase 2.5: 程式碼簡化（必須）Code Simplification (REQUIRED)

**⚠️ 阻塞型步驟：此步驟必須完成才能建立 PR。**

在建立 PR 前，**必須**使用 `code-simplifier` 技能檢查並簡化程式碼。
Before creating a PR, you **must** use the `code-simplifier` skill to check and simplify code.

**為何重要 Why Important**：

- 減少 Code Review 階段的修改建議
- 提升程式碼可讀性和維護性
- 確保符合專案程式碼風格
- 降低後續 token 消耗

**執行步驟 Execution Steps**：

1. **識別變更檔案**

    ```bash
    # 檢視此分支的所有變更檔案
    git diff master..HEAD --name-only | grep -E '\.(ts|js)$'
    ```

2. **執行程式碼簡化技能**
    - 閱讀 `code-simplifier` 技能文件
    - 對變更的 TS/JS 檔案執行簡化
    - 確保遵循專案 coding standards

3. **簡化完成標準**
    - [ ] 無不必要的巢狀結構
    - [ ] 無冗餘程式碼和抽象
    - [ ] 變數和函式命名清晰
    - [ ] 無描述顯而易見程式碼的註解
    - [ ] 測試通過且功能不變

4. **提交簡化變更**
    ```bash
    git add .
    git commit -m "refactor: simplify code for PR readiness"
    git push
    ```

> 💡 **Agent 整合**：輸入「簡化程式碼」、「refactor」或 `@code-simplifier` 觸發技能。

> ❌ **禁止跳過**：未完成程式碼簡化不得進入 Phase 3 建立 PR。

---

### Phase 3: 建立 Pull Request Create PR

#### 3.1 分析分支歷史

```bash
# 查看此分支相對於 master 的所有 commits
git log master..HEAD --oneline

# 查看變更的檔案清單
git diff master..HEAD --stat
```

#### 3.2 生成 PR 描述

**PR 描述模板**：

```markdown
## 變更摘要 Summary

{1-3 句描述主要變更}

## 相關 Spec Related Spec

- Spec: `/specs/{NNN}-feature-name/spec.md`
- Tasks: `/specs/{NNN}-feature-name/tasks.md`

## 變更類型 Type of Change

- [ ] 🐛 Bug 修復 (non-breaking change which fixes an issue)
- [ ] ✨ 新功能 (non-breaking change which adds functionality)
- [ ] 💥 破壞性變更 (fix or feature that would cause existing functionality to change)
- [ ] 📝 文件更新 (documentation only changes)

## 變更內容 Changes

- {變更 1}
- {變更 2}
- {變更 3}

## 測試計劃 Test Plan

- [ ] `npm run test` 通過
- [ ] `npm run lint` 通過
- [ ] `npm run compile` 成功
- [ ] 手動測試：{測試項目}

## 螢幕截圖 Screenshots (if applicable)

{如有 UI 變更，附上截圖}
```

#### 3.3 建立 PR

```bash
# 使用 GitHub CLI 建立 PR
gh pr create --title "feat(scope): description" --body-file pr-description.md

# 或互動式建立
gh pr create

# 指定 reviewer（選用）
gh pr create --reviewer username1,username2
```

#### 3.4 檢查 PR 狀態

```bash
# 查看目前 PR
gh pr view

# 查看 CI 檢查狀態
gh pr checks
```

---

### Phase 4: 等待 Code Review 並發布（強制）Wait for Review & Release (REQUIRED)

**⚠️ 阻塞型步驟：PR 建立後必須立即進入此階段，不可中斷流程。**

PR 建立完成後，**必須**立即執行 `pr-review-release` 技能來監聽 Copilot Code Review 結果並完成後續發布流程。

#### 4.1 請求 Copilot Review（若尚未配置）

```bash
# 請求 Copilot Code Review
gh pr edit --add-reviewer copilot-pull-request-reviewer
```

#### 4.2 啟動 Review 監聽

```powershell
# 執行輪詢腳本等待 Copilot Review 完成
.\.github\skills\pr-review-release\scripts\poll-review.ps1

# 自訂參數（逾時 60 分鐘，每 30 秒查詢一次）
.\.github\skills\pr-review-release\scripts\poll-review.ps1 -TimeoutMinutes 60 -PollIntervalSeconds 30
```

#### 4.3 根據 Review 結果執行後續流程

| Review 狀態              | Exit Code | 後續動作                              |
| ------------------------ | --------- | ------------------------------------- |
| `COMMENTED` / `APPROVED` | 0         | 評估建議 → 修正（如需）→ Merge → 發布 |
| `CHANGES_REQUESTED`      | 1         | 必須修正 → 重新推送 → 重新等待 Review |
| 逾時                     | 2         | 手動檢查 PR 狀態                      |

#### 4.4 執行 pr-review-release 技能

Review 監聽完成後，**強制**進入 `pr-review-release` 技能的完整流程：

1. **Phase 1**: 評估 Review 建議（採納/忽略）
2. **Phase 2**: 程式碼修正（如有採納的建議）
3. **Phase 3**: 程式碼簡化（阻塞型）
4. **Phase 4**: Git 操作（Merge PR、清理分支）
5. **Phase 5**: 發布流程（版本號、CHANGELOG、Tag、Release）

> 💡 **Agent 整合**：輸入「處理 code review」、「merge PR」或 `@pr-review-release` 觸發技能。

> ❌ **禁止中斷**：完成 PR 建立後不可中止流程，必須完成到發布為止。

---

## SDD 整合指南 SDD Integration Guide

### 在 Spec 分支工作時

1. **開發前**：確認 spec 文件齊全

    ```bash
    ls specs/{NNN}-feature-name/
    # 應有：spec.md, plan.md, tasks.md, research.md, data-model.md
    ```

2. **開發中**：按 tasks.md 的 Phase 順序 commit

    ```bash
    git commit -m "feat(blocks): [T025] implement esp32_wifi_connect block"
    ```

3. **開發完成**：使用本技能建立 PR（自動觸發 Review 監聽）

4. **Review 完成**：本技能自動執行 `pr-review-release` 處理 merge 和發布

### Commit Message 與 Task 關聯

```bash
# 關聯 tasks.md 中的任務編號
git commit -m "feat(generators): [T032] implement WiFi connect generator with 10s timeout"

# 多任務完成
git commit -m "feat(i18n): [T072-T086] add translations for all 15 languages"
```

---

## 快速指令 Quick Commands

### 一鍵 Commit + Push

```bash
# 分析變更並提交
git add .
git commit -m "$(git diff --cached --stat | head -1 | sed 's/^ //')"
git push
```

### 一鍵建立 PR

```bash
# 從目前分支建立 PR 到 master
gh pr create --fill --base master
```

---

## 檢查清單 Checklist

### Commit 前 Before Commit

- [ ] 變更已通過 `npm run lint`
- [ ] 變更已通過 `npm run test`
- [ ] 變更已通過 `npm run compile`
- [ ] Commit message 符合 Conventional Commits 格式
- [ ] Scope 正確反映變更範圍

### 程式碼簡化階段（阻塞型）Before Code Simplification

- [ ] 已識別所有變更的 TS/JS 檔案
- [ ] 已執行 code-simplifier 技能
- [ ] 無不必要的巢狀結構
- [ ] 無冗餘程式碼和抽象
- [ ] 變數和函式命名清晰
- [ ] 無描述顯而易見程式碼的註解
- [ ] 測試通過且功能不變
- [ ] 簡化變更已提交並推送

### 建立 PR 前 Before PR Creation

- [ ] **程式碼簡化已完成（必須）**
- [ ] 分支已推送到遠端
- [ ] PR 描述清楚說明變更內容
- [ ] 已關聯相關 Spec（如適用）
- [ ] 測試計劃已列出

### PR 建立後 After PR Creation

- [ ] CI 檢查通過
- [ ] 已請求 Copilot Code Review
- [ ] Review 監聽腳本已啟動
- [ ] **→ Review 完成後自動執行 `pr-review-release` 技能**

### 發布階段 Release Phase

- [ ] Review 建議已評估處理
- [ ] 程式碼修正已完成（如需）
- [ ] 程式碼簡化已完成（阻塞型）
- [ ] PR 已 Squash Merge
- [ ] 版本號已更新
- [ ] CHANGELOG 已更新
- [ ] Git Tag 已建立
- [ ] GitHub Release 已建立

---

## 相關資源 Related Resources

- [Anthropic commit-commands plugin](https://github.com/anthropics/claude-code/tree/main/plugins/commit-commands) - 本技能靈感來源
- [Conventional Commits 規範](https://www.conventionalcommits.org/zh-hant/)
- [GitHub CLI 文件](https://cli.github.com/manual/)
- [pr-review-release 技能](../pr-review-release/SKILL.md) - PR 審查後的下一步
- [code-simplifier 技能](../code-simplifier/SKILL.md) - PR 前程式碼簡化（必須）

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shen-ming-hong) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

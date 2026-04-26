---
name: auto-dev-setup
description: 設定 Auto-Dev workflow，實現 Human-in-the-Loop 自動化開發 Use when this capability is needed.
metadata:
  author: miles990
---

# Auto-Dev Setup Skill

為任何專案設定 Human-in-the-Loop 自動開發流程。

## 使用時機

當用戶說：
- "幫我設定 auto-dev"
- "我想在這個專案用自動開發"
- "設定 GitHub Actions 自動開發流程"

## 設定流程

### Step 1: 確認需求

使用 AskUserQuestion 確認：

```
1. Workflow 來源
   □ 使用 Reusable Workflow（推薦，自動更新）
   □ 複製完整 Workflow（可自訂）

2. Skills 來源
   □ 使用 claude-software-skills
   □ 使用自己的 skills repo
   □ 不使用額外 skills

3. 額外設定
   □ 需要任務佇列（定時處理）
   □ 需要 Feedback 處理（PR 上繼續迭代）
```

### Step 2: 建立目錄結構

```bash
mkdir -p .github/workflows
mkdir -p .claude/memory/{learnings,failures,decisions,patterns,strategies}
mkdir -p .github/ISSUE_TEMPLATE
```

### Step 3: 建立 Workflow（二擇一）

#### 方式 A: Reusable Workflow（推薦）

```yaml
# .github/workflows/auto-dev.yml
name: 🤖 Auto-Dev

on:
  issues:
    types: [labeled]
  issue_comment:
    types: [created]
  workflow_dispatch:
    inputs:
      goal:
        description: '開發目標'
        required: true

jobs:
  auto-dev:
    uses: {SKILLS_REPO}/.github/workflows/auto-dev-reusable.yml@main
    with:
      goal: ${{ github.event.inputs.goal || '' }}
    secrets:
      ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
```

替換 `{SKILLS_REPO}` 為實際的 repo 路徑。

#### 方式 B: 完整 Workflow

從 claude-software-skills 複製：
- `.github/workflows/auto-dev.yml`
- `.github/workflows/auto-dev-feedback.yml`
- `.github/workflows/auto-dev-queue.yml`

### Step 4: 建立 Issue Template

```yaml
# .github/ISSUE_TEMPLATE/auto-dev.yml
name: 🤖 Auto-Dev Task
description: 建立一個自動開發任務
labels: ["auto-dev"]
body:
  - type: textarea
    id: goal
    attributes:
      label: 目標
      description: 描述開發目標
    validations:
      required: true
```

### Step 5: 初始化 Memory

```markdown
# .claude/memory/index.md
# 專案記憶索引

## 最近學習
<!-- LEARNINGS_START -->
<!-- LEARNINGS_END -->

## 失敗經驗
<!-- FAILURES_START -->
<!-- FAILURES_END -->
```

### Step 6: 提醒設定 Secret

告知用戶：
```
請到 Repository Settings → Secrets → Actions
新增 ANTHROPIC_API_KEY
```

## 使用方式速查

| 操作 | 方式 |
|------|------|
| 觸發自動開發 | Issue + `auto-dev` label |
| 命令觸發 | 留言 `/evolve [目標]` |
| 繼續迭代 | PR 上留言 `/evolve [調整]` |
| 手動觸發 | Actions → Run workflow |

## 驗證設定

設定完成後，建議：

1. 建立測試 Issue
2. 加上 `auto-dev` label
3. 確認 Action 正確觸發
4. 確認 PR 正確建立

## 相關文檔

- [AUTO-DEV.md](https://github.com/miles990/claude-software-skills/blob/main/.github/AUTO-DEV.md)
- [Evolve Skill](../../skills/evolve/SKILL.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/miles990) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

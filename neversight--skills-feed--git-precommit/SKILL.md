---
name: git-precommit
description: 提交前編排器。觸發：commit、提交、推送、做完了、收工。 Use when this capability is needed.
metadata:
  author: neversight
---

# Git 提交前工作流（編排器）

## 觸發條件

| 用戶說法 | 觸發 |
|----------|------|
| 準備 commit、要提交了 | ✅ |
| 推送、做完了、收工 | ✅ |

---

## 可用工具

### 編排的 Skills

| Step | Skill | 工具 | 必要性 |
|------|-------|------|--------|
| 1 | memory-updater | `memory_bank_update_progress` | **必要** |
| 2 | readme-updater | `read_file`, `replace_string_in_file` | 可選 |
| 3 | changelog-updater | `read_file`, `replace_string_in_file` | 可選 |
| 4 | roadmap-updater | `read_file`, `replace_string_in_file` | 可選 |
| 5 | ddd-architect | `grep_search`, `list_dir` | 條件觸發 |
| 6 | - | `run_in_terminal` (git commands) | **必要** |

### Git 操作工具

| 工具 | 用途 |
|------|------|
| `get_changed_files()` | 取得變更檔案清單 |
| `run_in_terminal("git status")` | 檢查 Git 狀態 |
| `run_in_terminal("git add .")` | 暫存變更 |
| `run_in_terminal("git commit -m '...'")` | 提交 |

---

## 執行流程

```
┌─────────────────────────────────────────────────┐
│              Git Pre-Commit Orchestrator        │
├─────────────────────────────────────────────────┤
│  Step 1: memory-sync     [必要] Memory Bank 同步 │
│  Step 2: readme-update   [可選] README 更新      │
│  Step 3: changelog-update[可選] CHANGELOG 更新   │
│  Step 4: roadmap-update  [可選] ROADMAP 更新     │
│  Step 5: arch-check      [條件] 架構文檔檢查     │
│  Step 6: commit-prepare  [最終] 準備提交         │
└─────────────────────────────────────────────────┘
```

---

## 標準工作流程

```python
# Step 1: 檢查變更
get_changed_files()

# Step 2: 同步 Memory Bank（必要）
memory_bank_update_progress(
    done=["完成功能 X"],
    doing=[],
    next=["下一步..."]
)

# Step 3: 分析是否需要更新文檔
# - 新功能 → 更新 README
# - 版本變更 → 更新 CHANGELOG
# - 里程碑完成 → 更新 ROADMAP

# Step 4: 準備提交
run_in_terminal("git add .")
run_in_terminal("git status")

# Step 5: 建議 commit message
# 格式：type(scope): description
# - feat: 新功能
# - fix: 修復
# - docs: 文檔
# - refactor: 重構

# Step 6: 執行提交（用戶確認後）
run_in_terminal("git commit -m 'feat: 新增功能'")
```

---

## 快速模式

```
「快速 commit」 = --quick

只執行：
1. Memory Bank 同步
2. git add + commit
```

---

## 輸出範例

```
🚀 Git Pre-Commit 工作流

[1/6] Memory Bank 同步 ✅
  └─ progress.md: 更新 2 項

[2/6] README 更新 ⏭️ (無變更)

[3/6] CHANGELOG 更新 ✅
  └─ 添加 v0.2.0 條目

[4/6] ROADMAP 更新 ⏭️ (無變更)

[5/6] 架構文檔 ⏭️ (無結構性變更)

[6/6] Commit 準備 ✅
  └─ 建議訊息：feat: 新增用戶認證模組

📋 Staged files:
  - src/auth/...

準備好了！確認提交？
```

---

## 相關技能

- `memory-updater` - Memory Bank 同步
- `changelog-updater` - CHANGELOG 更新
- `readme-updater` - README 更新
- `roadmap-updater` - ROADMAP 更新

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

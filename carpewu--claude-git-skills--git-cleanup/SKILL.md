---
name: git-cleanup
description: Cleans up merged Git branches safely. Use when cleaning up old branches, deleting merged branches, or maintaining repository hygiene. Use when this capability is needed.
metadata:
  author: carpewu
---

# Git Cleanup

清理已合并的分支

## Context

- 当前分支: !`git branch --show-current`
- 本地分支: !`git branch`
- 已合并到 develop: !`git branch --merged develop 2>/dev/null`
- 已合并到 main: !`git branch --merged main 2>/dev/null`
- 远程分支: !`git branch -r | head -30`

---

## Protected Branches

以下分支**永远不删除**：
- `main`
- `master`
- `develop`
- `dev`
- `release/*`（当前活跃的）

---

## Workflow

### Step 1: 扫描分支

分析可清理的分支：

```
🧹 分支清理扫描

📋 可安全删除的分支（已合并）:

本地分支:
  ✓ feature/old-feature      (合并于 2025-01-20)
  ✓ bugfix/login-fix         (合并于 2025-01-18)

远程分支:
  ✓ origin/feature/old-feature
  ✓ origin/bugfix/login-fix

🔒 保护分支（不删除）:
  • main
  • develop

⚠️ 未合并分支（跳过）:
  • feature/work-in-progress
```

---

### Step 2: 确认删除

```
🗑️ 将删除以下分支:

本地: 2 个
  • feature/old-feature
  • bugfix/login-fix

远程: 2 个
  • origin/feature/old-feature
  • origin/bugfix/login-fix

请选择操作:
  • 输入 "all" → 删除所有
  • 输入 "local" → 仅删除本地
  • 输入 "remote" → 仅删除远程
  • 输入分支名 → 删除指定分支
  • 输入 "cancel" → 取消
```

---

### Step 3: 执行删除

```bash
# 删除本地分支
git branch -d feature/old-feature
git branch -d bugfix/login-fix

# 删除远程分支
git push origin --delete feature/old-feature
git push origin --delete bugfix/login-fix

# 清理远程引用
git fetch --prune
```

---

### Step 4: 输出结果

```
✅ 分支清理完成!

🗑️ 已删除:
├─ 本地: 2 个分支
│  • feature/old-feature
│  • bugfix/login-fix
└─ 远程: 2 个分支
   • origin/feature/old-feature
   • origin/bugfix/login-fix

🔒 保留:
├─ main
├─ develop
└─ feature/work-in-progress (未合并)

💡 提示: 使用 git fetch --prune 定期清理远程引用
```

---

## Safety Measures

1. **只删除已合并分支**：使用 `git branch --merged`
2. **保护核心分支**：main/develop 永不删除
3. **确认后执行**：不自动删除，等待用户确认
4. **使用 -d 而非 -D**：-d 只删除已合并分支，更安全

---

## Error Handling

| 情况         | 处理                                     |
| ------------ | ---------------------------------------- |
| 无可清理分支 | `✨ 没有需要清理的分支，仓库很整洁！`     |
| 删除当前分支 | `❌ 无法删除当前分支，请先切换到其他分支` |
| 远程删除失败 | 继续删除其他，最后汇报失败的分支         |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/carpewu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

---
name: git-github
description: React Native (Expo) 项目的 Git 和 GitHub 操作，包括分支管理、提交、推送、创建仓库、Pull Request、合并流程。当用户提到"git"、"github"、"提交"、"推送"、"分支"、"PR"、"合并"、"仓库"、"rebase"时使用此 skill。 Use when this capability is needed.
metadata:
  author: aidenreed937
---

# Git 和 GitHub 操作

提供 React Native/Expo 项目的 Git 版本控制和 GitHub 仓库管理的完整工作流。

## 快速参考

### 核心原则

| 原则                       | 说明                                    |
| -------------------------- | --------------------------------------- |
| **禁止直接 push main**     | main 只能通过 PR 合并                   |
| **先 rebase 后 PR**        | 提交前必须 rebase 到最新远程分支        |
| **使用 rebase merge**      | PR 合并统一使用 `--rebase` 保持线性历史 |
| **提交前必须通过质量检查** | 参考 `code-quality` skill               |

### ⚠️ 提交前必须通过质量检查

**每次提交/推送前必须运行并通过：**

```bash
pnpm tsc --noEmit           # 0 type errors
pnpm eslint .               # 0 errors, 0 warnings
pnpm prettier --check .     # 0 format issues
pnpm jest                   # All tests passed
```

详细检查清单见 `.claude/skills/code-quality/SKILL.md`

### 分支命名

| 前缀        | 用途     | 示例                   |
| ----------- | -------- | ---------------------- |
| `feature/`  | 新功能   | `feature/user-auth`    |
| `fix/`      | Bug 修复 | `fix/login-bug`        |
| `hotfix/`   | 紧急修复 | `hotfix/critical-bug`  |
| `refactor/` | 重构     | `refactor/auth-module` |

### 提交格式（Conventional Commits）

```
<type>(<scope>): <subject>

类型: feat | fix | docs | style | refactor | perf | test | build | ci | chore
```

### 常用命令速查

```bash
# 分支
git checkout -b feature/xxx          # 创建分支
git branch -d feature/xxx            # 删除分支

# Rebase
git fetch origin develop && git rebase origin/develop

# 推送
git push -u origin feature/xxx       # 首次推送
git push --force-with-lease          # rebase 后推送

# PR
gh pr create --base develop          # 创建 PR
gh pr merge <n> --rebase --delete-branch  # 合并 PR
```

---

## 子代理执行（强烈推荐）

**Git 操作应通过子代理执行**：

```typescript
Task({
  subagent_type: 'general-purpose',
  description: '提交当前分支变更',
  prompt: `执行 Git 提交，遵循 .claude/skills/git-github/SKILL.md`,
});
```

**原因**：Git 操作需读取 diff/status/log（消耗大量 token），子代理可隔离处理。

---

## 工作流程

### Feature 分支流程

```bash
# 1. 从 develop 创建分支
git checkout develop && git pull
git checkout -b feature/xxx

# 2. 开发完成后同步 develop
git fetch origin develop
git rebase origin/develop

# 3. ⚠️ 质量检查（必须全部通过！）
pnpm tsc --noEmit           # 必须 0 errors
pnpm eslint .               # 必须 0 issues
pnpm prettier --check .     # 必须 0 issues
pnpm jest                   # 必须全部通过

# 4. 提交（质量检查通过后）
git add . && git commit -m "feat: xxx"

# 5. ⚠️ 推送前再次 rebase 远程（确保最新）
git fetch origin develop
git rebase origin/develop
git push --force-with-lease origin feature/xxx

# 6. 创建 PR
gh pr create --base develop --title "feat: xxx"

# 7. 合并（使用 rebase merge）
gh pr merge <n> --rebase --delete-branch
```

### 发布流程（Develop → Main）

```bash
# 1. 确保 develop 最新
git checkout develop && git pull

# 2. 质量检查
pnpm tsc --noEmit && pnpm eslint . && pnpm jest

# 3. 创建发布 PR
gh pr create --base main --head develop --title "release: v1.x.x"

# 4. 合并到 main
gh pr merge <n> --rebase

# 5. 打 tag
git checkout main && git pull
git tag -a v1.x.x -m "Release v1.x.x"
git push origin v1.x.x
```

### Hotfix 流程

```bash
# 1. 从 main 创建
git checkout main && git pull
git checkout -b hotfix/xxx

# 2. 修复后创建 PR 到 main
gh pr create --base main

# 3. 合并后同步到 develop
git checkout develop && git merge main && git push
```

---

## PR 规范

### 标题格式

```
<type>(<scope>): <description>
```

### 内容模板

```markdown
## Summary

- 变更点 1
- 变更点 2

## Test plan

- [ ] 验证项 1
- [ ] 验证项 2

🤖 Generated with [Claude Code](https://claude.com/claude-code)
```

---

## 冲突解决

| 文件类型                 | 策略                                           |
| ------------------------ | ---------------------------------------------- |
| `package.json`           | 保留较新版本，重新 `pnpm install`              |
| `pnpm-lock.yaml`         | 选择一方后重新 `pnpm install`                  |
| `app.json/app.config.ts` | 优先保留 main 结构                             |
| 代码文件                 | 根据业务逻辑手动合并                           |
| `ios/` / `android/`      | 谨慎处理，可能需要 `npx expo prebuild --clean` |

---

## .gitignore 规范

React Native/Expo 项目应忽略：

```gitignore
# Dependencies
node_modules/
.pnpm-store/

# Expo
.expo/
dist/
web-build/

# Native (如果使用 CNG 不提交)
# android/
# ios/

# Environment
.env
.env.*
!.env.example

# IDE
.vscode/
.idea/

# OS
.DS_Store
Thumbs.db

# Build
*.jks
*.p8
*.p12
*.key
*.mobileprovision
*.orig.*

# Metro
.metro-health-check*

# Testing
coverage/
```

---

## 安全提醒

| 禁止                   | 替代方案                                             |
| ---------------------- | ---------------------------------------------------- |
| 直接 push main         | 通过 PR 合并                                         |
| 合并未通过检测的代码   | 先 `pnpm tsc --noEmit && pnpm eslint . && pnpm jest` |
| `--force` 推送 main    | 使用 `--force-with-lease`（仅 feature 分支）         |
| 提交 `.env` 等敏感信息 | 使用 `.gitignore` 排除                               |
| 提交 API Keys/Secrets  | 使用环境变量                                         |

---

## 附录

### A. 完整提交示例

```bash
git commit -m "$(cat <<'EOF'
feat(auth): 添加 JWT token 刷新机制

- 添加 token 过期检测
- 实现自动刷新逻辑

Closes #123

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>
EOF
)"
```

### B. GitHub CLI 命令

```bash
# 认证
gh auth status

# 仓库
gh repo create <name> --private --source=. --push

# PR
gh pr list
gh pr view <n>
gh pr merge <n> --rebase --delete-branch  # 统一使用 rebase merge
```

### C. 分支流向图

```
feature/xxx ─┐
fix/xxx ─────┼──► develop ──► main (发布)
refactor/xxx ┘        ▲
                      │
hotfix/xxx ───────────┴──► main
```

### D. Expo 特殊注意

- 如果使用 CNG（Continuous Native Generation），`android/` 和 `ios/` 目录可以不提交
- 如果提交原生目录，确保 `.gitignore` 正确配置
- 原生配置改动优先通过 Config Plugin 管理，保持可复现性

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aidenreed937) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

---
name: git-release
description: | Use when this capability is needed.
metadata:
  author: chogng
---

# Git Release Skill

自动化 Git 项目版本发布流程，完成从代码提交到版本发布的所有步骤。

## 核心理解

**当用户说"更新版本"时，不是仅更新版本号，而是执行完整的版本发布流程**：
1. ✅ 提交所有当前修改（`git add -A && git commit`）
2. ✅ 更新版本号（修改 package.json 和 server/package.json）
3. ✅ 生成/更新 CHANGELOG（可选）
4. ✅ 创建版本 tag（`git tag vX.Y.Z`）
5. ✅ 推送到远程仓库（`git push && git push --tags`）

**不要**只更新版本号文件而不提交其他修改！这是错误的理解。

## 重要：必须询问版本类型

在执行发布前，**必须使用 AskUserQuestion 工具询问用户版本升级类型**：

```
问题：本次发布是什么类型的版本升级？

选项：
- patch (补丁版本) - Bug 修复、小改动 (例: 1.0.0 → 1.0.1)
- minor (小版本) - 新功能、向后兼容 (例: 1.0.0 → 1.1.0)
- major (大版本) - 破坏性变更、重大更新 (例: 1.0.0 → 2.0.0)
```

除非用户在请求中已明确指定版本类型（如"大版本更新"、"patch release"），否则必须询问。

## 完整发布流程（按顺序执行）

### 1. 检查前置条件

```bash
# 确认工作区状态（查看是否有未提交的修改）
git status --porcelain

# 确认当前分支
git branch --show-current

# 获取当前版本号
grep '"version"' package.json

# 获取最新 tag
git describe --tags --abbrev=0 2>/dev/null || echo "无现有 tag"

# 查看最近的 commit 历史（用于编写 commit 消息）
git log --oneline -5
```

### 2. 询问版本类型

**必须询问用户**，提供以下选项：

| 类型 | 描述 | 版本变化示例 |
|------|------|-------------|
| patch | 补丁版本：Bug 修复、文档更新、小改动 | 1.2.3 → 1.2.4 |
| minor | 小版本：新功能、向后兼容的改进 | 1.2.3 → 1.3.0 |
| major | 大版本：破坏性变更、API 重构 | 1.2.3 → 2.0.0 |

如果用户说"小更新"/"小版本" → minor
如果用户说"大更新"/"大版本" → major
如果用户说"修复"/"补丁" → patch

### 3. 更新版本号

根据用户选择的版本类型，更新项目中的版本号文件：

```bash
# 更新主项目的 package.json
# 更新 server/package.json（如果存在）
# 确保多个 package.json 文件版本保持一致
```

手动编辑 `package.json` 中的 `version` 字段，计算新版本号。

### 4. 提交所有修改（包括版本更新）

**关键步骤**：提交所有当前工作区的修改，包括：
- 用户之前做的所有代码修改
- 刚才更新的版本号文件
- 任何新增的未追踪文件

```bash
# 提交所有修改（包括新文件和版本号更新）
git add -A
git commit -m "chore(release): vX.Y.Z

[简要描述本次版本包含的主要更新内容]

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

**commit 消息应该**：
- 格式：`chore(release): vX.Y.Z`
- 包含本次版本的主要更新内容说明
- 遵循项目的 commit 风格

### 5. 创建版本 tag

```bash
git tag -a vX.Y.Z -m "Release vX.Y.Z"
```

Tag 命名必须使用 `vX.Y.Z` 格式（如 v0.1.1）。

### 6. 推送到远程仓库

**默认执行推送**，将 commit 和 tag 都推送到远程：

```bash
# 推送当前分支
git push

# 推送新创建的 tag
git push --tags
```

如果推送失败（如网络问题、权限问题），报告给用户并提供解决建议。

### 7. 生成 CHANGELOG（可选）

如果项目有 CHANGELOG.md 文件，可以更新它。按 Angular 规范分组 commits：

```markdown
## [X.Y.Z] - YYYY-MM-DD

### Breaking Changes
- 破坏性变更描述

### Features
- feat(scope): description

### Bug Fixes
- fix(scope): description
```

如果生成了 CHANGELOG，需要额外提交并推送：
```bash
git add CHANGELOG.md
git commit -m "docs: update CHANGELOG for vX.Y.Z"
git push
```

## 版本类型关键词映射

用户可能使用的表达方式：

| 用户说 | 版本类型 |
|-------|---------|
| 大版本、大更新、重大更新、breaking | major |
| 小版本、小更新、新功能、feature | minor |
| 补丁、修复、bugfix、hotfix、小改动 | patch |

## 注意事项

- **完整流程**：版本发布是一个完整流程，包括提交所有修改、更新版本号、创建 tag、推送。不要只执行部分步骤！
- **提交优先**：始终先提交所有当前工作区的修改，再进行版本号更新
- **默认推送**：完成 commit 和 tag 后，默认推送到远程仓库（除非用户明确说不推送）
- **版本号同步**：如果项目有多个 package.json（如 server/package.json），确保所有版本号保持一致
- **破坏性变更**：必须升级 major 版本
- **Tag 格式**：统一使用 `vX.Y.Z` 格式（如 v0.1.1、v1.0.0）
- **首次发布**：建议从 `0.1.0` 或 `1.0.0` 开始
- **测试检查**：发布前建议确保测试通过（如果有自动化测试）

## 参考文档

- **Angular Commit 规范**: 见 `references/angular-commit.md`
- **语义化版本指南**: 见 `references/semver-guide.md`
- **CHANGELOG 模板**: 见 `assets/CHANGELOG.template.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chogng) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

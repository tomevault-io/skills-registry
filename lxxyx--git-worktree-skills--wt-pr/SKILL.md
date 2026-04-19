---
name: wt-pr
description: 使用 GitHub CLI 创建 Pull Request。自动检测 PR 是否已存在，生成简洁的 PR 标题和描述，提交未提交的更改。触发条件：用户输入 "/wt-pr" 或请求创建 PR/提交代码到 GitHub。 Use when this capability is needed.
metadata:
  author: lxxyx
---

# wt-pr

使用 GitHub CLI 创建 Pull Request，自动检测已有 PR、提交未提交更改、生成简洁 PR 描述。

## 工作流程

1. **检查 PR 是否已存在**
2. **提交未提交的更改**（如有）
3. **生成 PR 标题和描述**
4. **推送分支并创建 PR**

## 执行步骤

### 1. 检查已有 PR

```bash
# 获取当前分支
git branch --show-current

# 检查是否已有 PR
gh pr list --head <current-branch> --json number,title,url
```

### 2. 提交未提交的更改

```bash
# 检查是否有未提交更改
git status --porcelain

# 如有更改，询问用户是否提交
# 用户确认后：
git add -A
git commit -m "<合适的 commit message>"
```

Commit message 生成规则：
- 基于变更文件和最近的 commit 历史
- 使用 Conventional Commits 格式
- 简洁描述变更内容

### 3. 生成 PR 信息

**标题格式**：`<type>: <简短描述>`

**描述格式**（简洁版）：
```markdown
## 变更内容
<!-- 1-2 句话概括变更 -->

## 测试情况
<!-- 简要说明测试情况 -->
```

生成依据：
- 最近 commit messages
- 变更文件列表（`git diff --name-only`）
- 分支名称（推断 type）

### 4. 创建/更新 PR

```bash
# 推送到远程
git push -u origin <current-branch>

# 创建 PR
gh pr create --title "<生成的标题>" --body "<生成的描述>"

# 或更新已有 PR（如有未提交更改已提交）
# gh pr edit <pr-number> --title "..." --body "..."
```

## 分支类型到 PR 标题的映射

| 分支前缀 | PR 标题前缀 |
|----------|-------------|
| feat/* | feat: |
| fix/* | fix: |
| refactor/* | refactor: |
| docs/* | docs: |
| style/* | style: |
| test/* | test: |
| perf/* | perf: |
| chore/* | chore: |

## 示例

当前分支：`feat/user-auth`
Commit: `添加用户登录接口`
生成的 PR：
- 标题：`feat: 添加用户登录接口`
- 描述：简要说明变更内容和测试情况

## 注意事项

- 需要已安装并配置 GitHub CLI (`gh`)
- 如果 PR 已存在且有新的提交，会自动更新 PR
- 遵循项目的 PR 模板（如有）

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lxxyx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

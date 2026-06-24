---
name: git-commit
description: Generates commit messages by analyzing git diffs and learning project commit style. Use when committing code changes, when the user asks for help writing commit messages, or mentions git commit.
metadata:
  author: carpewu
---

# Git Commit

智能 Git 提交 - 学习项目风格，生成规范 commit message

## Context

- 当前分支: !`git branch --show-current`
- Git 状态: !`git status --short`
- 暂存变更统计: !`git diff --cached --stat`
- 暂存变更详情: !`git diff --cached`
- 提交历史样本: !`git log --format="%s" -15`

---

## Workflow

执行以下 3 个步骤，不要跳过任何步骤。

### Step 1: 前置检查与分析

#### 1.1 前置检查

首先检查以下条件，如不满足则**立即停止并提示用户**：

| 检查项 | 失败时的处理 |
|--------|-------------|
| 暂存区为空 | 停止并提示：`暂存区为空，请先使用 git add 添加文件` |
| 在 main/master 分支 | 警告：`⚠️ 当前在保护分支，建议创建新分支后再提交` |
| 检测到敏感文件 | 警告并列出文件，建议排除 |

**敏感文件模式**（检测暂存区）：
```
.env, .env.*, *.env
*credential*, *secret*, *token*, *password*
*.pem, *.key, *.p12, *.pfx
id_rsa, id_dsa, id_ecdsa
```

#### 1.2 学习项目提交风格

分析 Context 中的「提交历史样本」，识别以下模式：

**语言判断**：
- 统计 15 条历史 commit 中的中文/英文比例
- 中文字符占比 > 50% → 使用中文
- 否则 → 使用英文

**格式判断**：

| 模式 | 识别特征 | 示例 |
|------|----------|------|
| Conventional + Scope | 匹配 `类型(范围): ` | `feat(api): xxx` |
| Conventional 无 Scope | 匹配 `类型: ` | `feat: xxx` |
| 方括号前缀 | 匹配 `[类型] ` | `[feat] xxx` |
| 无前缀 | 无固定前缀 | `Add user login` |

**Scope 提取**：从历史中提取常用 scope 列表，如：`api`, `ui`, `core`, `auth`, `docs`

#### 1.3 分析暂存变更

根据 `git diff --cached` 判断变更类型：

| 变更类型 | 识别信号 |
|----------|----------|
| `feat` | 新增文件、新函数/类/组件、新 API 端点 |
| `fix` | 修复条件判断、异常处理、边界情况 |
| `refactor` | 重命名、提取函数、移动代码（功能不变） |
| `docs` | .md 文件、注释、README |
| `test` | test_*.py、*.spec.ts、*.test.js |
| `chore` | package.json、配置文件、CI/CD |
| `style` | 纯格式化、空格、缩进 |

**Scope 推断**（根据文件路径）：
- `src/api/*` → api
- `src/ui/*` → ui
- `src/core/*` → core
- 跨多模块 → 不加 scope

---

### Step 2: 生成预览

根据 Step 1 的分析结果，生成 commit message 并展示预览。

**输出格式**：

```
╭─────────────────────────────────────────────────────╮
│  📋 Git 提交预览                                    │
╰─────────────────────────────────────────────────────╯

🎨 检测到的项目风格:
   语言: [中文/英文]
   格式: [feat(scope): / feat: / 无前缀]
   常用 Scope: [api, ui, core, ...]

📝 生成的提交信息:
╭─────────────────────────────────────────────────────╮
│ <type>(<scope>): <subject>                          │
╰─────────────────────────────────────────────────────╯

📁 将提交的文件:
   • file1.py  (+xx, -xx)
   • file2.py  (+xx, -xx)
   [共 N 个文件, +XX 行, -XX 行]

⚠️ 注意事项:
   [如有敏感文件警告或其他提示，在此显示]

─────────────────────────────────────────────────────
请选择操作:
  • 输入 "ok" 或 "确认" → 执行提交
  • 输入修改意见 → 重新生成
  • 输入 "cancel" 或 "取消" → 放弃操作
```

**Commit Message 生成规则**：
1. 使用检测到的项目风格（语言、格式）
2. Subject 长度 ≤ 50 字符
3. 动词开头（中文：添加/修复/重构；英文：add/fix/refactor）
4. 不加句号结尾

---

### Step 3: 确认执行

等待用户输入，根据响应执行：

| 用户输入 | 操作 |
|----------|------|
| `ok`/`确认`/`y`/`yes` | 执行 `git commit -m "..."` |
| 修改意见（如"改成fix"） | 根据意见重新生成，返回 Step 2 |
| `cancel`/`取消`/`n`/`no` | 输出 `🚫 已取消提交` 并结束 |

**执行成功后输出**：
```
✅ 提交成功!

📝 feat(api): 添加用户注册接口
🔑 commit: a1b2c3d
🌿 分支: feature/user-auth
```

---

## Default Conventions

如果项目提交历史不足 5 条，无法判断风格，使用以下默认规范：

```
格式: <type>(<scope>): <subject>
语言: 中文
动词: 祈使句（添加/修复/更新/重构/删除）

类型:
  feat     新功能
  fix      问题修复
  refactor 重构
  docs     文档
  test     测试
  chore    构建/工具/配置
  style    代码格式
  perf     性能优化
```

---

## Error Handling

| 情况 | 输出 |
|------|------|
| 暂存区为空 | `❌ 暂存区为空\n💡 请先使用 git add <file> 添加要提交的文件` |
| 在保护分支 | `⚠️ 当前在 main 分支\n💡 建议: git checkout -b feature/xxx` |
| 检测到敏感文件 | `⚠️ 检测到敏感文件:\n  ❌ .env.local\n💡 请使用 git reset HEAD <file> 移除` |
| git 命令失败 | `❌ Git 命令执行失败\n💡 错误信息: <stderr>` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/carpewu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

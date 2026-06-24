---
name: git-pr
description: Prepares Pull Request content by analyzing branch differences. Use when creating PRs, when the user needs help writing PR descriptions, or mentions pull request. Use when this capability is needed.
metadata:
  author: carpewu
---

# Git PR

准备 Pull Request - 生成标题和描述

## Context

- 当前分支: !`git branch --show-current`
- 远程地址: !`git remote get-url origin 2>/dev/null`
- 与 develop 的差异提交: !`git log origin/develop..HEAD --oneline 2>/dev/null || git log develop..HEAD --oneline 2>/dev/null`
- 变更文件统计: !`git diff origin/develop --stat 2>/dev/null || git diff develop --stat 2>/dev/null`
- 提交详情: !`git log origin/develop..HEAD --format="%s%n%b---" 2>/dev/null | head -50`

---

## Workflow

### Step 1: 确定目标分支

**检查当前分支类型**：

| 当前分支 | 目标分支 |
|----------|----------|
| `feature/*` | develop |
| `bugfix/*` | develop |
| `hotfix/*` | main |
| `release/*` | main |

如果用户指定了目标分支，使用指定的分支。

---

### Step 2: 分析变更

从 Context 中的提交记录分析：
1. **提取变更类型**：统计 feat/fix/refactor 等
2. **识别主要变更**：最重要的 1-3 个功能点
3. **统计影响范围**：变更文件数、代码行数

---

### Step 3: 合规检查

```
📋 PR 合规检查:

├─ [✓/✗] 分支命名规范
├─ [✓/✗] 目标分支正确（非直接 PR 到 main）
├─ [✓/✗] 无敏感文件
├─ [✓/!] 变更规模（大变更建议拆分）
├─ [?] 是否关联 Issue
└─ [?] 是否涉及数据库变更
```

**警告情况**：
- 变更超过 500 行：建议拆分
- 检测到 migration 文件：提示需要 DBA 审批
- 目标是 main 但非 hotfix：警告

---

### Step 4: 生成 PR 内容

输出以下格式，用户可直接复制：

```
╭─────────────────────────────────────────────────────╮
│  🔀 Pull Request 准备完成                           │
╰─────────────────────────────────────────────────────╯

📊 变更概览:
   分支: feature/user-auth → develop
   提交: 5 个
   文件: 8 个 (+356, -42)

⚠️ 注意事项:
   • 变更较大，建议重点 review auth.py

═══════════════════════════════════════════════════════
📋 复制以下内容到 PR:
═══════════════════════════════════════════════════════

## 标题

feat(auth): 实现用户认证模块

## 描述

### 🎯 变更目的

实现用户注册和登录功能，支持 JWT 认证。

### 📝 主要变更

- 新增用户注册接口 `POST /api/v1/users/register`
- 新增用户登录接口 `POST /api/v1/users/login`
- 实现 JWT token 生成和验证
- 添加认证中间件
- 补充单元测试

### 🔗 关联

Closes #123

### ✅ 自检清单

- [x] 代码符合项目规范
- [x] 添加/更新了测试
- [x] 本地测试通过
- [ ] 更新了相关文档

═══════════════════════════════════════════════════════
```

---

## Error Handling

| 情况 | 处理 |
|------|------|
| 当前在 main/develop | `❌ 当前在 develop 分支，无需创建 PR` |
| 无差异提交 | `❌ 当前分支与 develop 无差异` |
| 无法获取远程 | 跳过链接生成，其他正常输出 |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/carpewu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

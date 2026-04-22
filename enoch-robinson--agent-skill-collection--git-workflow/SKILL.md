---
name: git-workflow
description: Git 工作流和版本控制最佳实践。当用户需要管理 Git 分支、编写提交信息、解决合并冲突、或遵循 Git 工作流规范时使用此技能。 Use when this capability is needed.
metadata:
  author: enoch-robinson
---

# Git Workflow

提供专业的 Git 版本控制工作流指导，确保团队协作高效有序。

## 分支策略

### 主要分支
- `main/master`：生产环境代码，始终保持可部署状态
- `develop`：开发主分支，集成最新功能

### 功能分支命名
```
feature/[issue-id]-简短描述    # 新功能
bugfix/[issue-id]-简短描述     # Bug修复
hotfix/[issue-id]-简短描述     # 紧急修复
refactor/简短描述              # 重构
docs/简短描述                  # 文档更新
```

## Commit 规范 (Conventional Commits)

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Type类型
| Type | 描述 |
|------|------|
| feat | 新功能 |
| fix | Bug 修复 |
| docs | 文档更新 |
| style | 代码格式（不影响逻辑） |
| refactor | 重构（非新功能/修复） |
| perf | 性能优化 |
| test | 测试相关 |
| chore | 构建/工具变更 |

### 示例
```
feat(auth): 添加 OAuth2.0 登录支持

- 集成 Google OAuth 认证
- 添加 token刷新机制
- 更新用户模型

Closes #123
```

## 常用工作流

### 开始新功能
```bash
git checkout develop
git pull origin develop
git checkout -b feature/123-user-auth
```

### 提交变更
```bash
git add .
git commit -m "feat(auth): 实现用户登录功能"
git push origin feature/123-user-auth
```

### 合并前更新
```bash
git fetch origin
git rebase origin/develop
# 解决冲突后
git push --force-with-lease
```

##冲突解决原则

1. **理解双方变更**：先了解冲突原因
2. **保留正确逻辑**：不要简单地选择一方
3. **测试验证**：解决后必须测试
4. **及时沟通**：复杂冲突与相关人员确认

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/enoch-robinson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

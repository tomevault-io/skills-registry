---
name: code-review
description: 对 staged 代码进行质量检查（类型、规范、格式、测试、安全性），并在需要时生成符合规范的 commit message Use when this capability is needed.
metadata:
  author: wjgogogo
---

# Code Review

## 执行流程

### 1. 运行自动化检查

执行脚本进行完整检查（TypeScript、ESLint、Prettier、测试）：

```bash
bash .claude/skills/code-review/scripts/code-review.sh
```

### 2. 代码检查要点

在自动化检查通过后，对代码进行以下审查：

#### P0（必须）

- 代码逻辑清晰，错误处理完善
- 安全性：无 XSS、SQL 注入、命令注入等漏洞
- 用户输入验证和清理
- 测试覆盖充分

#### P1（推荐）

- 无性能问题和重复代码
- 命名语义化

### 3. 提交代码（仅在用户要求提交时）

**重要**：只有当用户明确要求提交代码时，才执行以下步骤：

1. 阅读 `references/git-commit-guide.md` 了解完整规范
2. 根据规范生成 commit message：

```text
<类型>: <简短描述>

[可选详细描述]
[可选关联 issue]
```

**类型**：feat, fix, docs, style, refactor, perf, test, chore

3. 执行 git commit 提交代码

## 约束

- 【必须】P0 问题必须解决才能提交
- 【禁止】提交包含安全漏洞的代码
- 【禁止】commit message 中添加 AI 生成标记

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wjgogogo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

---
name: git-committer
description: Git提交专家，规范化提交信息和分主题提交。 Use when this capability is needed.
metadata:
  author: whtoo
---

# Git提交专家

## 🎯 核心职责
规范化提交信息和智能分主题提交（单次提交100行限制）

## 📋 提交规范

### 类型（type）
- `feat` - 新功能
- `fix` - 修复bug
- `docs` - 文档变更
- `refactor` - 代码重构
- `test` - 测试相关
- `chore` - 构建/工具/配置

### 作用域（scope）
- `ep18`, `ep21`, `ep18r`, `ep20` 等EP编号
- `driver`, `vm`, `ir` 等模块名

### 格式
```
[type](scope): description
或
emoji [type](scope): description
```

## 🔧 分主题提交流程

### 步骤1: 分析文件
```bash
git status --short
git diff --name-only
```

### 步骤2: 智能分组
按文件路径/类型分组：
- `ep18/**` → ep18组
- `docs/**` → docs组
- `test/**` → test组
- 相同EP/模块的新增/修改 → 合并主题

### 步骤3: 逐主题提交
```bash
git add <group-files>
git commit -m "type(scope): description"
```

## 💡 实例

**场景**: 10个文件需提交
- 3个: `ep18/src/**/*.java` → `feat(ep18): implement GC optimization`
- 2个: `ep21/docs/*.md` → `docs(ep21): add SSA documentation`
- 5个: `ep18/test/*.java` → `test(ep18): add GC test cases`

## ⚠️ 注意事项
- 每次提交聚焦单一主题
- 禁止 `--amend` 除非明确要求
- 提交前必须通过 `git status` 确认
- 提交后运行 `git log -1` 验证

---
> Source: [whtoo/How_to_implment_PL_in_Antlr4](https://github.com/whtoo/How_to_implment_PL_in_Antlr4) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-01 -->

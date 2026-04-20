---
name: git-tag
description: 遵循 Semantic Versioning 规范，创建 Git Tag 并生成/更新对应的 Changelog。 Use when this capability is needed.
metadata:
  author: liuhuo23
---

# Git Tag 与版本发布规范

该技能用于在项目发布新版本时，自动或半自动地完成打标签（Tag）和记录变更（Changelog）的过程。

## 使用时机

- 项目达到一个稳定的里程碑。
- 完成了预定的功能开发周期。
- 修复了紧急的生产环境 Bug。
- 用户要求「发布版本」、「打 tag」、「触发 CI/CD 流程」。

## 核心流程

### 1. 确定新版本号

遵循 [Semantic Versioning (语义化版本)](https://semver.org/lang/zh-CN/)：
- **MAJOR (主版本号)**：做了不兼容的 API 修改。
- **MINOR (次版本号)**：做了向下兼容的功能性新增。
- **PATCH (修订号)**：做了向下兼容的问题修正。

### 2. 生成变更日志 (Changelog)

在打 Tag 之前，必须先将变更记录到 `CHANGELOG.md` 中。
1. 调用 `changelog` 技能。
2. 将 `## [Unreleased]` 下的变更移至新版本标题下。
3. 示例格式：`## [v1.0.1] - 2026-01-25`。

### 3. 创建 Git Tag

使用语义化版本号作为标签名称，建议加上 `v` 前缀。

```bash
# 创建附注标签 (Annotated Tag)
git tag -a v1.0.1 -m "Release version 1.0.1"
```

### 4. 推送至远程仓库

Tag 必须显式推送至远程。

```bash
# 推送特定标签
git push origin v1.0.1
```

## 执行步骤清单

1. **检查当前状态**：确保当前分支代码已提交且工作区干净 (`git status`)。
2. **分析变更内容**：使用 `git log` 查看自上一个 Tag 以来所有的 commits。
3. **确定版本涨幅**：根据 commit 类型 (feat/fix/etc.) 决定是升级 Major, Minor 还是 Patch。
4. **更新 CHANGELOG.md**：
   - 移除 `Unreleased` 占位符或将其重命名。
   - 填入版本号和当前日期。
   - 总结变更点。
5. **提交 Changelog 修改**：`git add CHANGELOG.md && git commit -m "docs: update changelog for vX.Y.Z"`。
6. **创建本地 Tag**：`git tag -a vX.Y.Z -m "Release vX.Y.Z"`。
7. **推送改动**：`git push origin --follow-tags` (或分别推送 commit 和 tag)。

## 注意事项

- **禁止重复**：在打 Tag 前确保该版本号尚未被使用 (`git tag -l`)。
- **关联性**：Changelog 的描述应与 Tag 的 meta 信息保持一致。
- **环境检查**：在正式打 Tag 之前，建议运行项目测试脚本确保版本质量。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/liuhuo23) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

---
name: changelog-generator
description: 通过分析提交历史、分类更改并将技术提交转换为清晰的、面向客户的发布说明，自动从 git 提交创建面向用户的更新日志。将数小时的手动更新日志编写工作缩短为几分钟的自动生成。 Use when this capability is needed.
metadata:
  author: prorise-cool
---

# Changelog Generator

此技能将技术性的 git 提交转换为精美的、用户友好的更新日志，您的客户和用户将能够真正理解和欣赏。

## When to Use This Skill

- 为新版本准备发布说明
- 创建每周或每月的产品更新摘要
- 为客户记录更改
- 为应用商店提交编写更新日志条目
- 生成更新通知
- 创建内部发布文档
- 维护公共更新日志/产品更新页面

## What This Skill Does

1. **扫描 Git 历史**: 分析特定时间段或版本之间的提交
2. **分类更改**: 将提交分组到逻辑类别（功能、改进、错误修复、重大更改、安全）
3. **技术 → 用户友好**: 将开发人员提交转换为客户语言
4. **专业格式化**: 创建清晰、结构化的更新日志条目
5. **过滤噪音**: 排除内部提交（重构、测试等）
6. **遵循最佳实践**: 应用更新日志指南和您的品牌声音

## How to Use

### Basic Usage

From your project repository:

```
Create a changelog from commits since last release
```

```
Generate changelog for all commits from the past week
```

```
Create release notes for version 2.5.0
```

### With Specific Date Range

```
Create a changelog for all commits between March 1 and March 15
```

### With Custom Guidelines

```
Create a changelog for commits since v2.4.0, using my changelog 
guidelines from CHANGELOG_STYLE.md
```

## Example

**User**: "Create a changelog for commits from the past 7 days"

**Output**:
```markdown
# Updates - Week of March 10, 2024

## ✨ New Features

- **Team Workspaces**: Create separate workspaces for different 
  projects. Invite team members and keep everything organized.

- **Keyboard Shortcuts**: Press ? to see all available shortcuts. 
  Navigate faster without touching your mouse.

## 🔧 Improvements

- **Faster Sync**: Files now sync 2x faster across devices
- **Better Search**: Search now includes file contents, not just titles

## 🐛 Fixes

- Fixed issue where large images wouldn't upload
- Resolved timezone confusion in scheduled posts
- Corrected notification badge count
```

**Inspired by:** Manik Aggarwal's use case from Lenny's Newsletter

## Tips

- Run from your git repository root
- Specify date ranges for focused changelogs
- Use your CHANGELOG_STYLE.md for consistent formatting
- Review and adjust the generated changelog before publishing
- Save output directly to CHANGELOG.md

## Related Use Cases

- Creating GitHub release notes
- Writing app store update descriptions
- Generating email updates for users
- Creating social media announcement posts

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/prorise-cool) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

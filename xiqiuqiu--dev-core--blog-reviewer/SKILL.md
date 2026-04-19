---
name: blog-reviewer
description: 博客内容审查与格式校正工具。用于审查blog目录下的Markdown/MDX文章，校正格式问题，并使用AI分析生成Front Matter（包括title、author、date、tags、summary、slug、fileName）。触发时机：(1)用户要求审查博客文章 (2)需要生成或修正Front Matter (3)需要校正Markdown/MDX格式 (4)批量处理博客文章 Use when this capability is needed.
metadata:
  author: xiqiuqiu
---

# Blog Reviewer

审查并优化blog目录下的Markdown/MDX文章。

## 功能

1. **Front Matter生成** - 自动生成/补全YAML元数据
2. **格式校正** - 按MDX规范校正文章格式
3. **AI分析** - 智能生成tags和summary

## Front Matter规范

```yaml
---
title: "文章标题"
author: "Anonymous"
date: "YYYY-MM-DD"
tags: ["tag1", "tag2", "tag3"]
summary: "AI生成的文章摘要，约100-150字"
slug: "url-friendly-slug"
fileName: "原文件名.md"
---
```

### 字段说明

| 字段     | 来源            | 说明              |
| -------- | --------------- | ----------------- |
| title    | 文件名/首行标题 | 提取并格式化      |
| author   | 默认值          | "Anonymous"       |
| date     | 文件修改时间    | 格式YYYY-MM-DD    |
| tags     | AI分析          | 3-5个相关技术标签 |
| summary  | AI分析          | 100-150字内容摘要 |
| slug     | 标题转换        | 小写、连字符      |
| fileName | 原文件名        | 保留.md扩展名     |

## MDX格式规范

### 必须遵循

1. **标题层级** - 文章以 `#` 一级标题开头，后续按层级递增
2. **代码块** - 使用三反引号，标注语言类型
3. **列表缩进** - 嵌套列表使用2空格缩进
4. **空行** - 段落间、标题后需空行
5. **Front Matter** - 必须位于文件开头，`---`包裹

### 常见问题修正

- 无Front Matter → 自动生成
- 标题无`#`前缀 → 添加前缀
- 代码块未标语言 → 推断并标注
- 列表格式不规范 → 校正缩进

## 工作流程

### 单文件审查

1. 读取文章内容
2. 检查Front Matter
   - 缺失：分析内容，AI生成tags/summary，创建完整Front Matter
   - 存在但不完整：补全缺失字段
3. 检查正文格式
   - 标题层级
   - 代码块格式
   - 列表缩进
4. 输出修正后文件
5. 执行索引生成脚本

### 批量审查

1. 扫描blog目录所有.md/.mdx文件
2. 对每个文件执行单文件审查流程
3. 执行索引生成脚本
4. 生成审查报告

## 索引生成

审查完成后，执行以下命令更新博客索引：

```bash
node scripts/generate-blog-data.js
```

**脚本功能**（`scripts/generate-blog-data.js`）：

- 扫描`/blog`目录下所有.md文件
- 提取Front Matter信息
- 生成`data/blog-posts.json`索引文件
- 按日期倒序排列
- 输出标签统计

## AI生成指导

### Tags生成

分析文章内容，提取3-5个核心关键词：

- 主要技术栈（React、Vue、Node.js等）
- 核心概念（性能优化、状态管理等）
- 应用场景（前端、后端、全栈等）

### Summary生成

撰写100-150字摘要：

- 概述文章主题
- 点明核心价值
- 适合作为博客列表预览

## 示例

### 输入文件

```markdown
前端性能优化实战：如何实现"乐观更新"

引言：从一个常见的聊天应用场景说起
...
```

### 输出文件

```markdown
---
title: "前端性能优化实战：如何实现"乐观更新""
author: "Anonymous"
date: "2026-01-19"
tags: ["React", "前端性能", "乐观更新", "用户体验"]
summary: "本文深入探讨乐观更新（Optimistic UI）技术，通过先假装成功再校对结果的策略，实现极致响应速度。文章详解三步工作流，提供React伪代码示例，并分析跨设备同步等局限性。"
slug: "frontend-performance-optimistic-ui"
fileName: "前端性能优化实战：如何实现"乐观更新".md"
---

# 前端性能优化实战：如何实现"乐观更新"

## 引言：从一个常见的聊天应用场景说起

...
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xiqiuqiu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

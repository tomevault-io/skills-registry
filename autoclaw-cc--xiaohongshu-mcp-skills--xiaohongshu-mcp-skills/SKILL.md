---
name: xhs-content-plan
description: | Use when this capability is needed.
metadata:
  author: autoclaw-cc
---

## 执行流程

### 1. 明确策划需求

向用户了解：
- 目标领域/赛道（如：美妆、旅行、美食）
- 策划目的：选题灵感 / 竞品分析 / 热门趋势

### 2. 搜索分析

根据需求调用 `search_feeds` 搜索相关内容：
- 使用不同关键词多次搜索覆盖领域
- 利用 `sort_by` 筛选：最多点赞（爆款）、最新（趋势）

对高互动笔记，调用 `get_feed_detail` 获取详情，分析：
- 标题写法和关键词
- 内容结构和篇幅
- 话题标签使用
- 评论区用户关注点

如需分析特定博主，调用 `user_profile` 查看其内容风格和数据表现。

### 3. 输出策划建议

整理分析结果，为用户提供：
- 热门选题方向
- 标题参考模板
- 推荐话题标签
- 内容结构建议

## 约束

- 这是只读分析 skill，不执行任何发布或互动操作
- 搜索操作需要已登录状态

---
> Source: [autoclaw-cc/xiaohongshu-mcp-skills](https://github.com/autoclaw-cc/xiaohongshu-mcp-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-23 -->

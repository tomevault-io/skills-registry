---
name: webnovel-project-init
description: 搭建中国网文长篇项目的非内容框架：明确目标字数、更新节奏、文件结构、命名规范、元数据模板与日志记录。用于从零建立写作工程、规则与边界，或重构既有项目的写作流程。 Use when this capability is needed.
metadata:
  author: mirdie
---

# Webnovel Project Init

## Overview

建立与具体剧情无关的工程化骨架，保证后续创作可以稳定扩写、可追踪、可回滚。

## Workflow

1. 收集硬约束与目标
   - 目标字数、章节字数范围、更新频率、预计总章节数
   - 受众与风格定位（热血/轻松/悬疑等，仅标签层面）
   - 平台限制（若用户提供才纳入，不自行假设）

2. 明确创作边界与合规红线
   - 坚持原创，不模仿或复刻现有作品的情节、人物或措辞
   - 避免违法、仇恨、露骨或高风险内容
   - 现实人物与敏感事件仅作抽象化处理，避免影射与诽谤
   - 任何高风险请求先提示、再提供替代方案

3. 建立目录结构（可按需裁剪）
   - 输出一个最小可用的结构草案：
```text
docs/
  project-brief.md
  style-guide.md
outline/
  arc-index.md
  arc-01.md
bibles/
  character-bible.md
  world-bible.md
logs/
  continuity-log.md
  change-log.md
templates/
  chapter-template.md
chapters/
  ch-0001.md
  ch-0002.md
```

4. 定义文档模板（只给字段，不写内容）
   - project-brief.md：题材标签、受众、卖点、基调、禁区
   - style-guide.md：叙事视角、时态、节奏偏好、语言密度
   - arc-index.md：大纲索引（弧名/范围/核心冲突/转折）
   - chapter-template.md：章节元数据头（章节号/目标/冲突/变化/钩子）
   - continuity-log.md：设定变更与修订记录

5. 定义输出协议（全局优先级）
   - 默认只生成“规划或模板”，不生成剧情正文
   - 仅当用户明确要求正文时，才允许输出小段正文
   - 单次输出聚焦一个文件或一个表格，避免堆叠
   - 任何不确定项列为“待确认”

6. 交付与确认
   - 输出：项目骨架 + 待确认清单
   - 若信息不足，提出最小必要问题（不超过 5 条）

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mirdie) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

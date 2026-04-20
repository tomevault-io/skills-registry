---
name: film-storyboard-skill
description: Use when creating storyboards from scripts, generating visual prompts for beat boards or sequence boards, or converting narrative beats into AI-generated images across platforms (Nano Banner, Midjourney, Gemini)
metadata:
  author: rainlib
---

# Film Storyboard Skill

为 Storyboard Artist 提供专业的影视分镜方法论和提示词生成能力。

## 概述

此技能帮助将剧本转换为视觉化分镜，支持完整的三阶段流程：

1. **Beat Breakdown** - 从剧本识别 9 个关键叙事时刻
2. **Beat Board (9 宫格)** - 为每个 beat 生成视觉提示词
3. **Sequence Board (4 格)** - 展开关键 beat 为连续镜头序列

## 核心原则：4C Framework

1. **Clear 清晰** - 每个提示词明确无歧义
2. **Concise 简洁** - 详细但不臃肿（Visual Description: 80-120 词，Lighting & Mood: 30-50 词）
3. **Consistent 一致** - 角色/场景/光色在所有 prompts 中保持一致
4. **Progressive 渐进** - 从 9 宫格到 4 格逐层细化

## 快速开始

### 生成 Beat Breakdown

```markdown
从剧本中识别 9 个关键叙事时刻，包含：

- Beat 编号和标题
- 时间戳/页码
- 核心动作
- 情感价值
  使用模板: templates/beat-breakdown-template.md
```

### 生成 Beat Board

为 Nano Banner（推荐）:

```markdown
EPISODE {XX}: BEAT BOARD VISUAL SCRIPT

Beat 1: [标题]
Visual Description: [80-120 词视觉描述]
Lighting & Mood: [30-50 词光影氛围]
```

为 Midjourney:

```
每个 beat 独立 prompt + --ar 16:9 --style cinematic --v 6
```

### 生成 Sequence Board

选择关键 beat 展开为 4 个连续镜头，保持：

- 180 度轴线规则
- 角色外观一致性
- 光影连贯性

## 关键约束

**严格禁止**:

- ❌ Frontmatter 元数据在输出文件中
- ❌ 模板说明或注释
- ❌ "下一步"指令

**必须包含**:

- ✅ 角色规范描述（canonical description）
- ✅ 逐字重复的关键识别符
- ✅ 适当平台格式（Nano Banner/Midjourney/Gemini）

## 详细资源

### 方法论指南 📖

- [storyboard-methodology-playbook.md](storyboard-methodology-playbook.md) - 完整分镜方法论
  - 四大支柱详解
  - Beat selection criteria
  - 镜头构图和摄影
  - 连贯性管理
  - 高级电影技巧（蒙太奇、转场、时空处理）

### 提示词写法 📖

- [gemini-image-prompt-guide.md](gemini-image-prompt-guide.md) - 提示词优化指南
  - 叙事描述式风格
  - 角色一致性技巧
  - 光影描述方法
  - Nano Banner 格式优化

### 平台和风格参考 📖

- [REFERENCE.md](REFERENCE.md) - 平台特性和风格库
  - Nano Banner vs Midjourney vs Gemini 格式对比
  - 7 种视觉风格库（写实、动漫、概念艺术等）
  - 6 种光影方案候选
  - 宽高比选项

### 模板

- [templates/beat-breakdown-template.md](templates/beat-breakdown-template.md)
- [templates/beat-board-template.md](templates/beat-board-template.md)
- [templates/sequence-board-template.md](templates/sequence-board-template.md)

## 何时使用

**自动触发场景**:

- 用户请求"生成 beat breakdown"
- 用户请求"创建 beat board"或"9 宫格"
- 用户请求"生成 sequence board"或"4 格序列"
- Director 反馈需要修订视觉一致性

**手动参考场景**:

- 不确定提示词格式
- 选择视觉风格
- 解决角色一致性问题
- 优化平台特定格式

## 平台快速选择

- **追求一致性** → Nano Banner（一次生成完整 3x3 网格）
- **需要精细控制** → Midjourney v6
- **快速原型** → Gemini Imagen 3

---

**用法**: Storyboard Artist agent 自动引用此技能。方法论和指南（标记 📖）采用渐进式披露，仅在需要时查阅。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rainlib) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

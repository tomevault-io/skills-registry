---
name: story-design
description: | Use when this capability is needed.
metadata:
  author: aicoder2048
---

# Story Design Skill

Generate story specification markdown files through an interactive workflow.

## Important: Path Convention

All `story_specs/` paths in this skill are relative to the **project root directory** (same level as `.claude/`), NOT relative to this skill directory.

## Workflow

### Step 1: Identify Genre

Map user's input to the correct genre. See [references/genres.md](references/genres.md) for the complete mapping.

### Step 2: Ask for Original Thought

Ask the user to provide their original story idea/thought:

```
请描述你的故事创意：
- 你想讲述一个怎样的故事？
- 有什么核心概念或想法想要表达？
- （可以简单描述，也可以详细展开）
```

### Step 3: Read Genre Sample

Read the corresponding sample file: `story_specs/<genres_type>_sample.md`

This file contains:
- Q1: 故事的核心创意和吸引力
- Q2: 核心冲突
- Q3: 故事调性（with genre-specific options）

### Step 4: Generate THREE Versions

Based on user's original thought and the sample file's Q1-Q3 framework, generate THREE distinct versions:

**Version 1: 经典版（Classical/Traditional）**
- Follows established genre conventions
- Familiar story patterns that readers expect
- Safe, proven narrative structures

**Version 2: 创新版（Creative/Innovative）**
- Fresh twists on genre tropes
- Unexpected combinations or perspectives
- Modern interpretations of classic themes

**Version 3: 颠覆版（Subversive/Revolutionary）**
- Challenges genre conventions
- Unconventional narrative approaches
- Unique, distinctive storytelling

Each version MUST include:
```markdown
## 版本 X: [版本名称]

### 故事标题
[Title in Chinese]

### 故事简介
[2-3 paragraph story introduction]

### Q1：故事的核心创意和吸引力
[Answer based on sample.md format]

### Q2：核心冲突
[Answer based on sample.md format]

### Q3：故事调性
[Answer based on sample.md format]

### 主要人物
| 姓名 | 身份 | 特征 | 与主线的关系 |
|------|------|------|--------------|
| ... | ... | ... | ... |
```

### Step 5: User Selection

Present all three versions and ask user to:
1. Pick one version (1, 2, or 3)
2. Optionally provide final comments or modifications

```
请选择一个版本（1/2/3），并可选择性地添加修改意见：
```

### Step 6: Output Final Spec

Write the final story spec to: `story_specs/<genres_type>_<story_title>.md`

Example output paths:
- `story_specs/武侠江湖_龙吟月下.md`
- `story_specs/科幻小说_文明的最后一个变量.md`
- `story_specs/童话故事_我的世界.md`

Incorporate any user modifications before writing the final file.

## Output Format

The final markdown file should follow this structure:

```markdown
# 《[故事标题]》

[Optional: Subtitle or tagline]

## Q1：故事的核心创意和吸引力
[Detailed answer]

## Q2：核心冲突
[Detailed answer with specific conflict points]

## Q3：故事调性
[Selected tone with explanation]

---

## 核心人物

### 主角
[Name and description]

### 主要人物群像
| 姓名 | 身份 | 特征 | 与主线的关系 |
|------|------|------|--------------|
| ... | ... | ... | ... |

### 世界观关键词
- [Keyword 1]
- [Keyword 2]
- ...

### 隐含主题
- [Theme 1]
- [Theme 2]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aicoder2048) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

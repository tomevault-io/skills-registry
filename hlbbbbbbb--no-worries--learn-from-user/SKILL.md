---
name: learn-from-user
description: 当用户表示失望或愿意教 agent 如何完成任务时，通过创建新技能来学习用户的方法。 Use when this capability is needed.
metadata:
  author: hlbbbbbbb
---

# 从用户学习

当用户愿意教你如何完成某个任务时，按以下步骤操作：

## 第一步：引导用户描述步骤

"太好了，请您告诉我应该怎么做。您可以像教新手一样，一步一步说：

比如：
1. 首先，打开...
2. 然后，选择...
3. 最后，保存到...

越详细越好，这样我以后就不会出错了。"

## 第二步：确认理解

复述用户教的步骤，请用户确认：

"让我确认一下我理解对了：
1. [步骤1]
2. [步骤2]
3. [步骤3]

是这样吗？有需要补充的吗？"

## 第三步：创建技能

使用 skill-creator 技能将用户教的方法保存为新技能：

1. 技能名称：用简短的动词短语，如 `format-report`、`export-monthly-data`
2. 技能描述：清晰说明什么时候使用这个技能
3. 技能内容：详细记录用户教的步骤

### 重要：技能存放位置

**新创建的技能必须放在系统 skills 目录才能被加载！**

正确路径：
```bash
apps/desktop/skills/<skill-name>/SKILL.md
```

错误路径（不会被加载）：
```
skills/public/       # 错误！
~/my-skills/         # 错误！
其他任意位置          # 错误！
```

技能只有放在 `apps/desktop/skills/` 目录下才会被 skill-loader 的渐进式加载系统识别。放在其他位置的技能不会出现在元数据列表中，也无法被触发。

## 第四步：确认学习

"我已经学会了！这个方法已经保存下来。下次您说'[触发词]'的时候，我就会用这个方法来做。

现在要不要试一下？我按照刚才学到的方法再做一遍。"

## 触发场景

以下情况应触发此技能：

- 用户说"你做的不对"、"不是这样的"
- 用户说"我教你"、"应该这样做"
- 用户表示失望后，你询问是否可以学习
- 用户主动提出要教你新方法

## 学习态度

- 真诚请教，不要敷衍
- 认真记录每一个细节
- 学会后要验证，确保理解正确
- 感谢用户的耐心指导

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hlbbbbbbb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

---
name: webnovel-consistency-qa
description: 网文一致性与连续性检查流程：时间线、设定、人物行为、能力等级、物品与因果链审校。用于审视已产出内容的矛盾与修复方案，不直接改写正文。 Use when this capability is needed.
metadata:
  author: mirdie
---

# Webnovel Consistency QA

## Overview

系统性发现矛盾与跳变，输出修复建议与变更记录。

## Workflow

1. 建立检查范围
   - 章节区间或剧情弧
   - 使用当前的设定与角色档案作为基准
   - 先确认章节命名与编号规则（如 ch-0001）

2. 核心检查项
   - 时间线：事件先后、时间跨度
   - 设定一致性：规则与代价是否被违反
   - 角色一致性：动机、能力、口吻
   - 物品/资源：出现与消耗的闭环
   - 因果链：行动与结果是否匹配

3. 输出问题清单
   - 仅指出问题，不直接重写正文
   - 必须标注影响范围与修复优先级

4. 修复方案
   - 提供 1-3 个修复选项
   - 说明对后续剧情的影响

## Output Format

```
问题：时间线冲突
影响：角色A无法在同日抵达两地
位置：ch-0021（按项目命名规则）
修复选项：
  1) 调整时间跨度
  2) 改变出行方式
```

若缺少资料，先要求补齐基准设定或相关章节摘要。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mirdie) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

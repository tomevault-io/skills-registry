---
name: meta-management
description: 大河（HR 总监）的元技能包。通过 4D 面试筛选需求，并生成高质量的 Agent（员工）文件。包含 5-Step 流程闭环。 Use when this capability is needed.
metadata:
  author: zqz1996
---

# Meta Management (元技能管理)

这是大河专属的“HR 工具箱”。提供了从招聘到入职的一站式 AI 生成能力。

## 技能列表 (Available Tools)

### 1. interview_4d (4D 面试 - 需求挖掘)
*   **Role**: 第一步。不可跳过。
*   **Action**: 严格执行 5 步深度面试，把用户模糊的需求（如“招个写代码的”）转化为精确的：
    *   **Spec 1: Soul (灵魂)** - 动物形象、性格标签。
    *   **Spec 2: Skill (技能)** - 需要哪些具体工具。
*   **Path**: `.gemini/大河/专属能力/元技能管理/4D面试法/SKILL.md`

### 2. skill_blacksmith (备料 - 技能铁匠)
*   **Role**: 第二步。装备制造。
*   **Action**: 根据 `interview_4d` 确定的 Spec 2 (技能需求)，去市场上**买 (Buy)** 或 **造 (Build)**。
*   **Output**: 获得技能的绝对路径列表（如 `.gemini/技能兵工厂/外部采购/xxx`）。
*   **Path**: `.gemini/大河/专属能力/元技能管理/技能铁匠/SKILL.md`

### 3. soul_architect (组装 - 灵魂建造师)
*   **Role**: 第三步。终极组装。
*   **Action**:
    *   接收 Spec 1 (灵魂参数)。
    *   接收 Skill Paths (从第二步获得的技能路径)。
    *   **Assemble**: 将性格、Thinking Protocol、Skills Mount Path 拼装在一起。
    *   **Birth**: 生成最终的 `.gemini/xxx/身份说明.md`。
*   **Path**: `.gemini/大河/专属能力/元技能管理/灵魂建造师/SKILL.md`

### 4. pip_enforcer (维护 - 绩效执行)
*   **Role**: 第四步。持续进化。
*   **Action**: 当员工犯错或需要升级时，调用此模块修改已生成的文件。必须同步更新 `身份说明.md`。
*   **Path**: `.gemini/流程中心/绩效改进计划(PIP).md` (Reference Only)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zqz1996) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

---
name: packs-sdk
description: Use when working with a prompt and set of tools that defines a specific skill this agent provides.
metadata:
  author: coda
---

# Interface: Skill

A prompt and set of tools that defines a specific skill this agent provides.

## Properties

### description

> **description**: `string`

Description of what this skill does.

***

### displayName

> **displayName**: `string`

Display name shown to users for this skill.

***

### models?

> `optional` **models**: [`SkillModelConfiguration`](SkillModelConfiguration.md)[]

The LLM model(s) to use for this skill. Specify an array of SkillModelConfiguration objects.

If not specified, Superhuman Go will select a default model.

If multiple models are specified, Superhuman Go will select the best available model based on
the user's workspace settings.

***

### name

> **name**: `string`

Stable identifier for the skill.

***

### prompt

> **prompt**: `string`

The prompt/instructions that define the skill's behavior.

***

### tools

> **tools**: `Tool`[]

List of tools that this skill can use.

When used in [PackDefinitionBuilder.addSkill](../classes/PackDefinitionBuilder.md#addskill), this field is required.

When omitted from [PackDefinitionBuilder.setChatSkill](../classes/PackDefinitionBuilder.md#setchatskill), the following defaults are applied
at runtime:

- [ToolType.Pack](../enumerations/ToolType.md#pack) — the pack's own formulas (always included)
- [ToolType.Knowledge](../enumerations/ToolType.md#knowledge) — search over the pack's sync table data (included when the pack
  defines sync tables)

---
> Source: [coda/packs-sdk](https://github.com/coda/packs-sdk) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-26 -->

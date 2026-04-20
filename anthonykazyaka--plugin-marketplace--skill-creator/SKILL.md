---
name: skill-creator
description: Guide for creating effective skills. This skill should be used when users want to create a new skill (or update an existing skill) that extends Claude's capabilities with specialized knowledge, workflows, or tool integrations. Use when this capability is needed.
metadata:
  author: anthonykazyaka
---

# Skill Creator

This skill provides guidance for creating effective skills.

## About Skills

Skills are modular, self-contained packages that extend Claude's capabilities by providing
specialized knowledge, workflows, and tools. Think of them as "onboarding guides" for specific
domains or tasks—they transform Claude from a general-purpose agent into a specialized agent
equipped with procedural knowledge that no model can fully possess.

### What Skills Provide

1. Specialized workflows - Multi-step procedures for specific domains
2. Tool integrations - Instructions for working with specific file formats or APIs
3. Domain expertise - Company-specific knowledge, schemas, business logic
4. Bundled resources - Scripts, references, and assets for complex and repetitive tasks

## Core Principles

### Concise is Key

The context window is a public good. Skills share the context window with everything else Claude needs: system prompt, conversation history, other Skills' metadata, and the actual user request.

**Default assumption: Claude is already very smart.** Only add context Claude doesn't already have. Challenge each piece of information: "Does Claude really need this explanation?" and "Does this paragraph justify its token cost?"

Prefer concise examples over verbose explanations.

### Set Appropriate Degrees of Freedom

Match the level of specificity to the task's fragility and variability:

**High freedom (text-based instructions)**: Use when multiple approaches are valid, decisions depend on context, or heuristics guide the approach.

**Medium freedom (pseudocode or scripts with parameters)**: Use when a preferred pattern exists, some variation is acceptable, or configuration affects behavior.

**Low freedom (specific scripts, few parameters)**: Use when operations are fragile and error-prone, consistency is critical, or a specific sequence must be followed.

Think of Claude as exploring a path: a narrow bridge with cliffs needs specific guardrails (low freedom), while an open field allows many routes (high freedom).

### Skill Structure

Every skill consists of:
- **SKILL.md** (required): YAML frontmatter + markdown instructions
- **Bundled resources** (optional): scripts/, references/, assets/ directories

**For detailed structure information:**
- See [references/skill-structure.md](references/skill-structure.md) for complete anatomy, resource types, and progressive disclosure patterns
- Read when you need to understand skill organization, resource categories, or optimization patterns

## Skill Creation Process

**High-level workflow:**

1. Understand the skill with concrete examples
2. Plan reusable skill contents (scripts, references, assets)
3. Initialize the skill (run init_skill.py)
4. Edit the skill (implement resources and write SKILL.md)
5. Validate the skill (run quick_validate.py)
6. Add skill to plugin/marketplace configuration
7. Iterate based on real usage

**For detailed step-by-step guidance:**
- See [references/skill-creation-process.md](references/skill-creation-process.md) for complete instructions on each step
- Read when you're actively creating or iterating on a skill

**Quick reference for proven patterns:**
- **Multi-step workflows**: See [references/workflows.md](references/workflows.md)
- **Output quality patterns**: See [references/output-patterns.md](references/output-patterns.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anthonykazyaka) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

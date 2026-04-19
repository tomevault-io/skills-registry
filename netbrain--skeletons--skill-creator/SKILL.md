---
name: skill-creator
description: Create, validate, and package Claude skills - modular packages that extend Claude's capabilities through specialized knowledge, workflows, and tool integrations. Use when this capability is needed.
metadata:
  author: netbrain
---

# Skill Creator

## Overview

The Skill Creator provides guidance for building skills that extend Claude's capabilities through specialized knowledge, workflows, and tool integrations. These modular packages function as "onboarding guides" for specific domains.

## Key Components

Skills consist of a required SKILL.md file plus optional bundled resources:

- **SKILL.md**: Contains YAML metadata (name, description) and markdown instructions
- **scripts/**: Executable code for deterministic, reusable tasks
- **references/**: Documentation loaded into context as needed
- **assets/**: Output files like templates and icons

## Design Principle

Skills employ progressive disclosure—metadata always loads (~100 words), SKILL.md triggers when relevant (<5k words), and bundled resources load on demand.

## Creation Process

**Step 1**: Gather concrete usage examples to understand functionality requirements.

**Step 2**: Analyze examples to identify reusable resources (scripts, references, assets).

**Step 3**: Run `scripts/init_skill.sh <skill-name> --path <path>` to generate the skill template structure.

**Step 4**: Edit the skill using imperative language, focusing on procedural knowledge beneficial to another Claude instance.

**Step 5**: Iterate based on real-world testing feedback.

## Best Practices

Write in imperative form rather than second person. Keep SKILL.md lean by moving detailed information to references files. Include scripts for repeatedly-written code. Avoid duplicating information across files.

## Available Scripts

### init_skill.sh
Creates a new skill from template with proper directory structure and example files.

Usage:
```bash
.claude/skills/skill-creator/scripts/init_skill.sh <skill-name> --path <path>
```

Example:
```bash
.claude/skills/skill-creator/scripts/init_skill.sh my-new-skill --path .claude/skills
```

### quick_validate.sh
Validates skill structure and metadata.

Usage:
```bash
.claude/skills/skill-creator/scripts/quick_validate.sh <skill_directory>
```

Example:
```bash
.claude/skills/skill-creator/scripts/quick_validate.sh .claude/skills/my-skill
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/netbrain) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

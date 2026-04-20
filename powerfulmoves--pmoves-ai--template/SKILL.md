---
name: template-skill
description: Template for creating new PMOVES.AI agent skills Use when this capability is needed.
metadata:
  author: powerfulmoves
---

# Template Skill

**Category**: Templates/Skill
**Version**: 1.0.0
**Status**: Stable

## Overview

This is a template SKILL.md file for creating new agent skills in PMOVES.AI. Copy this directory and replace the placeholder content with your skill's specific implementation.

## Capabilities

List of key capabilities with emoji markers:
- ✨ Provides a standardized template for skill creation
- 🔍 Includes validation scripts to ensure compliance
- 🛠️ Contains example tool and cookbook implementations

## Skill Structure

```
.claude/skills/[skill-name]/
├── SKILL.md              # This file
├── tools/                # Implementation tools
│   └── example.py       # Tool implementations
├── prompts/              # Prompt templates
│   └── example.md
├── cookbook/             # Usage examples
│   └── examples.md
└── README.md             # Additional context
```

## Trigger Phrases

| Natural Language Phrase | Action | Tool |
|-------------------------|--------|------|
| "validate skill" | Validates SKILL.md format | validate_skills.py |
| "create skill" | Creates new skill from template | tools/create.py |
| "list skills" | Lists all available skills | tools/list.py |

## Tools

### validate_skills.py

**Purpose**: Validates SKILL.md files for compliance with PMOVES.AI patterns

**Usage**:
```bash
python skills/validate_skills.py path/to/SKILL.md
```

**Parameters**:
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| file | string | Yes | Path to SKILL.md file |
| --strict | flag | No | Fail on warnings |
| --fail-fast | flag | No | Stop at first error |

**Output**: Validation report with errors, warnings, and summary

### example.py

**Purpose**: Example tool implementation template with proper patterns

**Usage**:
```bash
python tools/example.py --help
```

## Configuration

Required environment variables or settings:

```yaml
PYTHONPATH: .  # For imports
LOG_LEVEL: INFO  # Logging level
```

## Cookbook

For detailed examples and workflows, see `cookbook/examples.md`.

### Quick Examples

**Example 1: [Use Case]**
```python
# Code example
result = tool1.execute(param1="value")
```

**Example 2: [Another Use Case]**
```python
# Code example
result = tool2.process(param2=123)
```

## Quick Reference

| Command | Purpose |
|---------|---------|
| `python tools/tool1.py` | Quick action |
| `python tools/tool2.py --help` | Help |

## Integration Points

- **NATS Subject**: `nats.subject.name`
- **API Endpoint**: `http://endpoint`
- **Database**: `connection_string`
- **MCP Server**: `server:tool`

## Troubleshooting

Common issues and solutions:

| Issue | Solution |
|-------|----------|
| [Error message] | [Fix steps] |
| [Another error] | [Fix steps] |

## See Also

- [Related Skill 1](../skill-1/SKILL.md)
- [Related Skill 2](../skill-2/SKILL.md)
- [Main Documentation](../../../docs/README.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/powerfulmoves) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

---
name: skill-creator
description: > Use when this capability is needed.
metadata:
  author: chenycl
---

# Skill Creator Guide

Create effective skills that extend Claude's capabilities with specialized knowledge, workflows, or tool integrations.

## Skill Structure

```
skill-name/
├── SKILL.md           # Core instructions (required)
├── scripts/           # Executable code (optional)
│   └── main.py
├── references/        # Extended documentation (optional)
│   └── details.md
└── assets/            # Templates, images, etc. (optional)
    └── template.txt
```

## SKILL.md Template

```yaml
---
name: skill-name
description: >
  Clear, concise description of what this skill does.
  Include trigger phrases that should activate this skill.
  Mention specific use cases: (1) first use case, (2) second use case.
  Triggers: "keyword1", "keyword2", "phrase that triggers".
---

# Skill Name

Brief overview of the skill's purpose and capabilities.

## Quick Start

### Basic Usage
```bash
# Example command or workflow
skill-command --option value
```

## Workflow

1. **Step One** - Description of first step
2. **Step Two** - Description of second step
3. **Step Three** - Description of third step

## Reference

| Item | Description |
|------|-------------|
| Key concept | Explanation |

## Examples

### Example 1: Common Use Case
```
Detailed example showing skill in action
```

## Notes

- Important consideration
- Another important note

## References

See [references/details.md](references/details.md) for extended documentation.
```

## Best Practices

### YAML Frontmatter

```yaml
---
name: lowercase-hyphenated
description: >
  CRITICAL: The description is how Claude decides when to use your skill.

  1. Start with WHAT the skill does
  2. List specific USE CASES with numbers: (1) case one, (2) case two
  3. Include TRIGGER PHRASES: "exact phrases", "keywords"
  4. Keep under 500 characters for the description
---
```

### Good vs Bad Descriptions

**Good:**
```yaml
description: >
  Generate professional code reviews following industry standards.
  Use when: (1) reviewing pull requests, (2) analyzing code quality,
  (3) finding bugs and security issues.
  Triggers: "review code", "check my PR", "code review", "find bugs".
```

**Bad:**
```yaml
description: A skill for doing code stuff.
```

### Skill Body Guidelines

1. **Keep it under 500 lines** - Split into references/ if longer
2. **Use clear headings** - Quick Start, Workflow, Reference, Examples
3. **Include examples** - Show real usage patterns
4. **Be specific** - Concrete steps, not vague instructions
5. **Link references** - Point to references/ for deep dives

### Scripts

```python
#!/usr/bin/env python3
"""
Script description.

Usage:
    python script.py <arg1> [--option value]
"""

import sys
import argparse
from pathlib import Path

def main():
    parser = argparse.ArgumentParser(description="Script description")
    parser.add_argument("input", help="Input file or value")
    parser.add_argument("--option", default="default", help="Optional parameter")
    args = parser.parse_args()

    # Main logic here
    print(f"Processing: {args.input}")

if __name__ == "__main__":
    main()
```

**Script Best Practices:**
- Use only Python stdlib when possible
- Document any pip dependencies
- Include --help support
- Make scripts executable (chmod +x)
- Use argparse for CLI interface

### References Directory

Use `references/` for:
- Detailed API documentation
- Extended examples
- Configuration options
- Troubleshooting guides

Keep main SKILL.md focused on quick start and common workflows.

## Skill Types

### Knowledge Skills
Provide domain expertise and best practices.
- Code style guides
- Industry standards
- Reference documentation

### Workflow Skills
Guide through multi-step processes.
- Document generation
- Analysis pipelines
- Review checklists

### Tool Skills
Integrate with external tools and APIs.
- Scripts for automation
- API integrations
- File processing

### Template Skills
Provide templates and boilerplate.
- Project scaffolding
- Document templates
- Code generators

## Testing Your Skill

1. **Upload to Claude.ai**
   ```bash
   cd skills/my-skill
   zip -r ../../my-skill.skill .
   ```
   Upload the .skill file in Settings → Capabilities → Skills

2. **Test triggers** - Try phrases from your description
3. **Test edge cases** - What happens with unusual inputs?
4. **Iterate** - Refine based on testing

## Common Mistakes

1. **Vague descriptions** - Be specific about triggers
2. **Too long** - Split into references if > 500 lines
3. **No examples** - Always include usage examples
4. **Missing triggers** - Add common phrases users might say
5. **Complex scripts** - Keep dependencies minimal

## Skill Naming

- Use lowercase
- Hyphen-separated words
- Descriptive but concise
- Examples: `code-review`, `git-commit`, `api-docs`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chenycl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

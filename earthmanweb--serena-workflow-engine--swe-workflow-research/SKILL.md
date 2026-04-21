---
name: swe-workflow-research
description: Code exploration and research without making changes Use when this capability is needed.
metadata:
  author: earthmanweb
---

## ⚠️ WORKFLOW INITIALIZATION

**If starting a new session**, first read workflow initialization:

```
mcp__plugin_swe_serena__read_memory("wf/WF_INIT")
```

Follow WF_INIT instructions before executing this skill.

---

# Workflow Research Skill

Explore and analyze codebase without making any changes.

## Purpose

- Understand code structure and patterns
- Find relevant files and functions
- Analyze dependencies and relationships
- Document findings for later use

## Actions

1. **Explore codebase** using Serena symbolic tools
2. **Search for patterns** using grep/glob
3. **Read relevant files** to understand implementation
4. **Document findings** in Skill Return section

## Restrictions

- **NO edits allowed** - read-only exploration
- **NO file creation** - documentation only
- Must update WORKING_MEMORY with findings

## Skill Return Format

```markdown
## Skill Return

- **Skill**: swe-workflow-research
- **Status**: [success|success_with_findings|needs_clarification]
- **Findings Summary**: [2-3 sentences describing what was found]
- **Artifacts**: [list of relevant files, patterns discovered]
- **Next Step Hint**: WF_CLASSIFY
```

## Exit

Output: `> **Skill /swe-workflow-research complete** - returning to WF_CLASSIFY`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/earthmanweb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

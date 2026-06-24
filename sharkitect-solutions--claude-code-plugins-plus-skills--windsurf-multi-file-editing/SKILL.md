---
name: windsurf-multi-file-editing
description: | Use when this capability is needed.
metadata:
  author: sharkitect-solutions
---
# Windsurf Multi File Editing

## Overview

This skill enables coordinated multi-file editing operations within Windsurf using Cascade AI. It provides atomic changes across multiple files, ensuring consistency when renaming symbols, moving code, or making cross-file refactoring changes.

## Prerequisites

- Windsurf IDE installed and configured
- Active Cascade AI subscription
- Project workspace initialized with `.windsurf/` directory
- Git or version control for rollback safety
- Understanding of project file structure and dependencies

## Instructions

1. **Scope the Operation**
2. **Configure Operation Template**
3. **Generate Preview**
4. **Execute with Preview**
5. **Verify Results**

See `${CLAUDE_SKILL_DIR}/references/implementation.md` for detailed implementation guide.

## Output

- Modified files with consistent changes applied
- `edit-history.json` log with operation details
- Rollback snapshot for recovery if needed
- Validation report with syntax check results

## Error Handling

See `${CLAUDE_SKILL_DIR}/references/errors.md` for comprehensive error handling.

## Examples

See `${CLAUDE_SKILL_DIR}/references/examples.md` for detailed examples.

## Resources

- [Windsurf Multi-File Editing Documentation](https://docs.windsurf.ai/features/multi-file-editing)
- [Cascade AI Coordination Guide](https://docs.windsurf.ai/cascade/coordination)
- [Atomic Operations Best Practices](https://docs.windsurf.ai/best-practices/atomic-ops)

---
> Source: [sharkitect-solutions/claude-code-plugins-plus-skills](https://github.com/sharkitect-solutions/claude-code-plugins-plus-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->

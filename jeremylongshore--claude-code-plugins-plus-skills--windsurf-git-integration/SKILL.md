---
name: windsurf-git-integration
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---
# Windsurf Git Integration

## Overview

This skill enables AI-assisted Git workflows within Windsurf. Cascade can generate commit messages from staged changes, suggest branch names, assist with merge conflict resolution, and automate common Git operations.

## Prerequisites

- Git installed and configured
- Windsurf IDE with Cascade enabled
- Git repository initialized
- SSH keys or HTTPS credentials configured
- Understanding of team Git workflow (GitFlow, trunk-based, etc.)

## Instructions

1. **Configure Git Credentials**
2. **Set Up AI Assistance**
3. **Install Git Hooks**
4. **Configure Team Standards**
5. **Train on Workflow**

See `${CLAUDE_SKILL_DIR}/references/implementation.md` for detailed implementation guide.

## Output

- Configured Git hooks
- AI-assisted commit messages
- Branch naming suggestions
- PR descriptions with context

## Error Handling

See `${CLAUDE_SKILL_DIR}/references/errors.md` for comprehensive error handling.

## Examples

See `${CLAUDE_SKILL_DIR}/references/examples.md` for detailed examples.

## Resources

- [Windsurf Git Integration](https://docs.windsurf.ai/features/git)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [Git Hooks Documentation](https://git-scm.com/docs/githooks)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

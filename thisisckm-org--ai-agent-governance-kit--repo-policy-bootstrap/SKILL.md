---
name: repo-policy-bootstrap
description: Bootstrap a repository with Codex and Claude Code governance files. Use when adding AGENTS.md, CLAUDE.md, coding-agent governance, secure SDLC rules, or repo-local AI assistant instructions to a software project. Use when this capability is needed.
metadata:
  author: ThisIsCKM-org
---

# Repo Policy Bootstrap

Add baseline governance files while preserving existing project instructions.

## Workflow

1. Inspect the target repository for existing `AGENTS.md`, `CLAUDE.md`, `.claude/`, policy, CI, and security files.
2. Preserve existing instructions and merge governance sections instead of overwriting meaningful local rules.
3. Add role boundaries, security, data protection, git workflow, human approval, testing, DevOps/Infra Controls, and observability sections.
4. Run validation after changes.
5. Report installed files and any manual follow-up.

## Required Output

- Files added or updated.
- Existing policies preserved.
- Validation result.
- Follow-up controls that belong in CI, branch protection, or identity systems.

---
> Source: [ThisIsCKM-org/ai-agent-governance-kit](https://github.com/ThisIsCKM-org/ai-agent-governance-kit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->

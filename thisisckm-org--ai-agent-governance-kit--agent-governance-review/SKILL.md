---
name: agent-governance-review
description: Review enterprise governance for Codex and Claude Code. Use when assessing AGENTS.md, CLAUDE.md, coding-agent policies, role boundaries, approval rules, tool restrictions, data-protection controls, or AI engineering governance gaps. Use when this capability is needed.
metadata:
  author: ThisIsCKM-org
---

# Agent Governance Review

Review governance as a layered control system: instruction files, repository policy, CI/CD enforcement, identity controls, auditability, and human oversight.

## Workflow

1. Identify the assistant scope: Codex, Claude Code, or both.
2. Read relevant `AGENTS.md`, `CLAUDE.md`, and repository policy files.
3. Compare the files with `references/governance-review-checklist.md`.
4. Separate soft controls from enforceable controls.
5. Report missing or weak controls with severity and concrete remediation.

## Required Output

- Scope reviewed.
- Findings ordered by risk.
- Missing required sections.
- Controls that need CI, branch protection, IAM, or scanner enforcement.
- Recommended next changes.

---
> Source: [ThisIsCKM-org/ai-agent-governance-kit](https://github.com/ThisIsCKM-org/ai-agent-governance-kit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->

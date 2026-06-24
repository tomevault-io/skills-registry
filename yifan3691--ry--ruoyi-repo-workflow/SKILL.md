---
name: ruoyi-repo-workflow
description: Use when working with repository structure, Git workflow, commits, pushes, repository moves, or agent-specific project conventions in this repository. Use whenever the user asks to commit, push, explain repo layout, move frontend or backend directories, or asks where project-local AI configuration should live.
metadata:
  author: yifan3691
---

# RuoYi Repo Workflow

Use this skill for repository operations and project-specific collaboration rules.

## Current repository facts

- The frontend lives inside the main repository at `ruoyi-vue/`.
- The old `ruoyi-vue/if-based-on-vue` sub-repository path is no longer the active layout.
- Pushes should go to the main repository remote unless the user explicitly asks for a different remote strategy.
- Commit messages in this project must be Chinese.

## Git workflow rules

- Inspect `git status` before any staging, commit, or push step.
- Stage only the files relevant to the requested task.
- Do not reset, discard, or overwrite unrelated user changes.
- Do not use force push unless the user explicitly asks for it.
- If a new branch is needed, follow the `codex/` prefix rule.

## Repository layout awareness

- Backend modules are rooted at `ruoyi-admin`, `ruoyi-system`, `ruoyi-common`, `ruoyi-framework`, `ruoyi-quartz`, `ruoyi-generator`, and `ods-business`.
- Frontend work belongs under `ruoyi-vue/`.
- Project-level Claude commands live under `.claude/commands/`.
- Project-level OpenCode commands live under `.opencode/command/`.
- Project-owned skills are stored under `.agents/skills/`.

## AI configuration rule

- Keep project-specific automation or instructions versioned inside the repository when they should travel with the project.
- Only mirror a project skill into a personal skill directory when local auto-discovery is needed on the current machine.

## Commit format

Prefer `类型: 中文说明`, for example:

- `feat: 新增客户管理查询接口`
- `fix: 修复登录页验证码刷新问题`
- `docs: 更新项目级 skills 说明`

---
> Source: [yifan3691/ry](https://github.com/yifan3691/ry) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->

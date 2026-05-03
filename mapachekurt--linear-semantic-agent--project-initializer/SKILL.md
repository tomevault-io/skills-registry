---
name: project-initializer
description: Scaffolds a new Google Antigravity project with the standard .agent harness, rules, and workflows. Use when this capability is needed.
metadata:
  author: mapachekurt
---

# Skill: Project Initializer

## Description
This skill bootstraps a new project with the mandatory ".agent" folder structure, including TDD workflows, debug modes, and coding standards.

## Usage
Run this skill when starting a new workspace to ensure compliance with personal best practices.

## Actions

1.  **Create Directories:**
    - `.agent/rules`
    - `.agent/skills`
    - `.agent/workflows`
    - `.agent/plans`

2.  **Create Core Files:**
    - Write `.agent/instructions.md` (System Instructions)
    - Write `.agent/rules/ui-rules.md` (UI Standards)
    - Write `.agent/rules/git-standards.md` (Git Standards)
    - Write `.agent/workflows/tdd.md` (TDD Workflow)
    - Write `.agent/skills/test-specialist.md` (Test Skill)
    - Write `.agent/skills/debug-mode.md` (Debug Skill)

3.  **Verify:**
    - Check that the files exist.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mapachekurt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

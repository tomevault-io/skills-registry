---
name: ai-tool-setup
description: Generates or updates AI tool instruction files for GitHub Copilot (copilot-instructions.md), Claude (CLAUDE.md), and Cursor (.cursorrules) based on the detected project type and existing playbook policies. Use when the user wants to add, update, or synchronize AI tool configuration across multiple assistants. Do not use for writing new skills, building application code, or general documentation tasks.
metadata:
  author: vanessamarely
---

# Skill: AI Tool Setup

## Purpose

Generate or update AI assistant instruction files for GitHub Copilot, Claude, and Cursor so all tools share a consistent set of policies derived from the playbook. Invoked when a developer wants to onboard a new AI tool or refresh existing configurations after playbook changes.

## Inputs

- `targetTool`: One of `copilot`, `claude`, `cursor`, or `all`
- `projectRoot`: Absolute path to the repository root
- `playbookRoot`: Path to the playbook directory (default: `ai-playbook`)

## Outputs

- `.github/copilot-instructions.md` — GitHub Copilot workspace instructions
- `CLAUDE.md` — Claude AI project instructions
- `.cursorrules` — Cursor IDE project rules
- Summary of files created or updated

## Procedures

### 1. Validate Inputs

Verify `projectRoot` exists and contains a recognizable project (check for `package.json`, `pom.xml`, or `pyproject.toml`).

Verify `playbookRoot` contains `.github/skills/` and `.github/copilot-instructions/`.

If either check fails, output an error and exit.

### 2. Detect Project Type

Execute: `node <playbookRoot>/tools/project-detect.mjs <projectRoot>`

Extract:
- `projectType` (react, node-typescript, java-maven, python, unknown)
- `framework`
- `language`

### 3. Load Applicable Policies

Always read: `<playbookRoot>/.github/copilot-instructions/workspace-policy.md`

If `projectType` is `react` or `node-typescript`:
- Read: `<playbookRoot>/.github/copilot-instructions/frontend-policy.md`
- Read: `<playbookRoot>/.github/copilot-instructions/backend-policy.md`

Read: `<playbookRoot>/.github/copilot-instructions/style-output.md`

### 4. Build Skill Routing Table

Map detected project types to skills:

| Project Type | Skills |
|---|---|
| `react` | `react-components`, `a11y-automation` |
| `node-typescript` | `node-typescript-service` |
| `java-maven` | backend-policy only (no dedicated skill yet) |
| `python` | backend-policy only (no dedicated skill yet) |

Read each applicable `SKILL.md` frontmatter to extract the `name` and one-line purpose.

### 5. Generate GitHub Copilot Instructions

If `targetTool` is `copilot` or `all`:

1. Check if `.github/copilot-instructions.md` exists in `projectRoot`.
2. If updating, preserve any project-specific custom sections marked with `<!-- custom -->` tags.
3. Generate content using the template: `assets/copilot-instructions.template.md`.
4. Write to `<projectRoot>/.github/copilot-instructions.md`.

### 6. Generate Claude Instructions

If `targetTool` is `claude` or `all`:

1. Check if `CLAUDE.md` exists in `projectRoot`.
2. If updating, preserve any custom sections.
3. Generate content using the template: `assets/claude.template.md`.
4. Write to `<projectRoot>/CLAUDE.md`.

### 7. Generate Cursor Rules

If `targetTool` is `cursor` or `all`:

1. Check if `.cursorrules` exists in `projectRoot`.
2. If updating, preserve any custom sections.
3. Generate content using the template: `assets/cursorrules.template.md`.
4. Write to `<projectRoot>/.cursorrules`.

### 8. Validate Scope

Execute: `node <playbookRoot>/tools/scope-guard.mjs <projectRoot> <allWrittenFiles>`

Abort if any file falls outside `projectRoot`.

### 9. Output Summary

List each file created or updated with its path and a one-line description of what it contains.

Suggest next steps:
- Reload the AI tool workspace or editor to pick up new instructions.
- Run `node <playbookRoot>/tools/project-detect.mjs <projectRoot>` to confirm detection.

## Error Handling

**Project root not found**: Output `ERROR: <projectRoot> does not exist.` and exit.

**Playbook not found**: Output `ERROR: Playbook directory not found at <playbookRoot>.` and exit.

**Unknown project type**: Generate generic instructions using workspace-policy only; note that project-specific skill routing was skipped.

**File write failure**: Report the path and OS error; do not partially update files.

## References

- Tool format details: `references/tool-formats.md`
- Skill routing guide: `references/skill-routing.md`

## Assets

- GitHub Copilot template: `assets/copilot-instructions.template.md`
- Claude template: `assets/claude.template.md`
- Cursor template: `assets/cursorrules.template.md`

---
> Source: [vanessamarely/ai-playbook-reposito](https://github.com/vanessamarely/ai-playbook-reposito) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->

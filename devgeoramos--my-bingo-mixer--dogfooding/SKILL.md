---
name: dogfooding
description: Dogfooding workflow for improving Copilot customizations with evidence from the current repository. Use when creating or updating copilot-instructions.md, AGENTS.md, instructions, prompts, or skills. Use when this capability is needed.
metadata:
  author: DevGeoRamos
---

# Dogfooding Customizations

## Outcome
Produce a high-signal customization update grounded in real repository conventions, with minimal duplication and clear next actions.

## When to Use
- Create or update workspace instructions.
- Create prompts, skills, or agents from a workflow already used in practice.
- Improve existing customization files that feel generic, stale, or duplicated.

## Procedure
1. Capture the target and scope.
- Confirm what file should be produced or updated.
- Decide scope: workspace (recommended for team use) or personal.
- If the request is underspecified, ask only the minimum clarifying questions.

2. Discover existing conventions first.
- Search for current customization files and core docs before writing anything.
- Prefer linking to docs over repeating long content.
- Detect whether a file should be created or merged.

3. Explore codebase facts with evidence.
- Collect exact build, test, and lint commands.
- Identify architecture boundaries and ownership by folder/file.
- Capture project-specific gotchas that are not obvious from generic best practices.

4. Verify critical points from primary sources.
- Validate subagent findings by reading key files directly.
- Resolve inconsistencies before editing.

5. Generate or merge with minimal surface area.
- Preserve useful existing sections.
- Remove stale or duplicated guidance.
- Keep instructions concise, actionable, and always-on only when truly global.

6. Add quality gates.
- Ensure names/paths/frontmatter are valid for the customization type.
- Ensure guidance is specific to this repo, not boilerplate.
- Ensure references point to existing files.

7. Close the loop.
- Summarize what was created or changed.
- Suggest 3-5 realistic example prompts that exercise the customization.
- Suggest related next customizations that naturally extend the workflow.

## Decision Points
- Existing file found:
  merge and refresh; do not replace blindly.
- No existing file:
  create the minimal template needed.
- Repo has rich docs:
  link to docs; do not embed large excerpts.
- Workflow unclear:
  ask for desired outcome, scope, and level of detail (checklist vs full workflow).

## Completion Checks
- The produced file is in the correct location and naming format.
- The guidance includes concrete repo commands and boundaries.
- The result avoids duplication and follows link-first documentation.
- The output includes clear examples for immediate use.

---
> Source: [DevGeoRamos/my-bingo-mixer](https://github.com/DevGeoRamos/my-bingo-mixer) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->

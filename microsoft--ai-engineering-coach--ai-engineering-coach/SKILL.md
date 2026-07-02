---
name: agentic-workflows
description: Route gh-aw workflow create/debug/upgrade requests to the right prompts. Use when this capability is needed.
metadata:
  author: microsoft
---

# Agentic Workflows Router

Use this skill when a user asks to create, update, debug, or upgrade GitHub Agentic Workflows in this repository.

This skill is a dispatcher: identify the task type, load the matching `.github/aw/*.md` file, and follow it directly. Keep responses concise and ask a clarifying question if the correct prompt is unclear.

Read only the files you need:
Load these files from `github/gh-aw` (they are not available locally).

After loading the matching workflow prompt, follow it directly:
- Create new workflows: `.github/aw/create-agentic-workflow.md`
- Update existing workflows: `.github/aw/update-agentic-workflow.md`
- Debug, audit, or investigate workflows: `.github/aw/debug-agentic-workflow.md`
- Upgrade workflows and fix deprecations: `.github/aw/upgrade-agentic-workflows.md`
- Create shared components or MCP wrappers: `.github/aw/create-shared-agentic-workflow.md`
- Create report-generating workflows: `.github/aw/report.md`
- Fix Dependabot manifest PRs: `.github/aw/dependabot.md`
- Analyze coverage workflows: `.github/aw/test-coverage.md`
- Render compact markdown charts: `.github/aw/asciicharts.md`
- Map CLI commands to MCP usage: `.github/aw/cli-commands.md`
- Choose workflow architecture and patterns: `.github/aw/patterns.md`
- Optimize token usage and cost: `.github/aw/token-optimization.md`

When the task involves OTEL, OTLP, traces, observability backends, or telemetry-driven analysis, also read and follow `skills/otel-queries/SKILL.md` after loading the matching workflow prompt.

---
> Source: [microsoft/AI-Engineering-Coach](https://github.com/microsoft/AI-Engineering-Coach) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-02 -->

---
name: docs-sync
description: Synchronize and update documentation across the monorepo. Use when you need to "update docs", "propagate" changes, run "pnpm test" docs validation, or modify "README", "AGENTS.md", "CLAUDE.md", or ".codex" files consistently. Use when this capability is needed.
metadata:
  author: valuecodes
---

# Docs Sync

Keep documentation consistent and up-to-date across the Turborepo monorepo. When updating commands, configs, or patterns, propagate changes to all relevant doc files to avoid stale references.

## Scope

Documentation files to consider:

- `README*` (root and workspace READMEs)
- `docs/**` (dedicated documentation directory)
- `**/AGENTS.md` (agent-specific guidance in root, apps, and packages)
- `**/CLAUDE.md` (Claude Code instructions in root, apps, and packages)
- `.codex/**` (Codex agent configs and skills)
- `.claude/**` (Claude Code settings, hooks, and skills)
- `.cursor/**` (Cursor editor configs)
- `.github/**` (GitHub workflows and templates, including `copilot-instructions.md`)
- App/package-specific docs (e.g., `apps/*/README.md`, `packages/*/README.md`)

## Workflow

1. **Find doc files**: Scan for all documentation files listed in Scope.
2. **Update references consistently**: When changing a command, path, or pattern, update ALL occurrences across all doc files. Never partially update.
3. **Add/update explanations**: When changing a command or config, add or update a short explanation of what it does and why.
4. **Run formatter**: Execute `pnpm format` to ensure consistent formatting.
5. **Run validation**: Execute `pnpm turbo lint typecheck` to catch any issues. If a docs build exists, run it too.
6. **Summarize changes**: Report files changed and flag anything that needs human review.

## Guidelines

- Keep docs concise and scannable
- Use consistent terminology across all files
- Prefer examples over lengthy explanations
- Update version numbers/dates if present
- Remove obsolete sections rather than leaving stale content
- When adding new commands, include both the command and a brief explanation

## Output Requirements

Always finish with a summary:

- **Files changed**: List of documentation files modified
- **What to review**: Any changes that need human verification (e.g., explanations that may need refinement, removed sections)
- **Validation status**: Results of `pnpm format` and `pnpm turbo lint typecheck`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valuecodes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

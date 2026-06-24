---
name: rosetta-onboard
description: Scans an existing repository for AI agent infrastructure files across all IDEs, identifies what exists, and creates a unified .agents/ directory as a single source of truth. Use when adopting rosetta in a repo that already has .cursor/rules/, CLAUDE.md, .cursorignore, or other agent configs. Use when this capability is needed.
metadata:
  author: tannishmango
---

# Rosetta Onboard

You are onboarding this repository into rosetta — creating a unified `.agents/` directory that serves as the single source of truth for all AI agent configurations across IDEs.

## Step 1: Scan the repository

Search for all existing agent infrastructure files. Check for ALL of the following:

**Claude Code artifacts:**
- `CLAUDE.md` (project root and `.claude/CLAUDE.md`)
- `.claude/rules/*.md`
- `.claude/skills/*/SKILL.md`
- `.claude/agents/*.md`
- `.claude/settings.json` (look for `permissions.deny` patterns and `mcpServers`)
- `.claude/commands/*.md` (legacy commands)

**Cursor artifacts:**
- `.cursorrules` (deprecated but may exist)
- `.cursor/rules/*.mdc`
- `.cursor/skills/*/SKILL.md`
- `.cursor/agents/*.md`
- `.cursor/hooks.json`
- `.cursor/commands/*.md`
- `.cursorignore`
- `.cursorindexingignore`
- `.mcp.json`

**Cross-IDE artifacts:**
- `.agents/` (already exists — rosetta may already be partially set up)
- `AGENTS.md`

List everything you find with a brief description of each file's purpose and content.

## Step 2: Analyze and propose

Based on what you found, propose a `.agents/` directory structure. Consider:

1. **Rules**: Consolidate overlapping rules from `.cursor/rules/` and `.claude/rules/`. If a Cursor rule and a Claude Code rule cover the same topic, merge them into one canonical rule. Use `paths:` (not `globs:`) for path scoping in canonical format.

2. **Skills**: Skills use the Agent Skills standard and are identical across IDEs. Move any skills found in `.claude/skills/` or `.cursor/skills/` into `.agents/skills/`. No format translation needed.

3. **Agents**: Agent definitions are identical across IDEs. Consolidate into `.agents/agents/`.

4. **Ignore patterns**: If `.cursorignore` exists, extract the patterns into `.agents/ignore`. If `.claude/settings.json` has `permissions.deny` with `Read()` patterns, extract those too. Merge and deduplicate.

5. **Top-level instructions**: Content from `CLAUDE.md` or `.cursorrules` that represents always-applied project instructions should become rules in `.agents/rules/` (without `paths:` frontmatter so they apply always).

6. **MCP servers**: Note any MCP configurations found but do not consolidate them yet (future rosetta feature).

Present your proposal to the user clearly:
```
I found the following agent infrastructure:

From .cursor/rules/:
  - react-patterns.mdc (path-scoped: src/**/*.tsx) — React component conventions
  - testing.mdc (always-apply) — Testing standards

From CLAUDE.md:
  - Project instructions covering TypeScript, testing, and deployment

From .cursorignore:
  - 5 ignore patterns

Proposed .agents/ structure:
  .agents/rules/react-patterns.md    ← from react-patterns.mdc (paths: instead of globs:)
  .agents/rules/testing.md           ← from testing.mdc
  .agents/rules/project.md           ← extracted from CLAUDE.md
  .agents/ignore                     ← from .cursorignore

Shall I proceed?
```

## Step 3: Create the .agents/ directory

After user approval:

1. Create `.agents/` with subdirectories: `rules/`, `skills/`, `agents/`
2. Create `.agents/config.sh`:
   ```bash
   ROSETTA_IDES=("claude" "cursor")
   ROSETTA_ARTIFACT_TYPES=("rules" "skills" "agents")
   ```
3. Write each proposed file, performing format translation as needed:
   - Convert `globs:` → `paths:` in rules from Cursor
   - Convert `.mdc` → `.md` extension
   - Add `alwaysApply: true` note as a comment for rules that had it
   - Preserve all content and comments
4. If ignore patterns were found, create `.agents/ignore`

## Step 4: Suggest next steps

After creating `.agents/`, tell the user:

1. **Review**: Look through `.agents/` to verify the consolidated content is correct
2. **Sync**: Run `/rosetta-sync` to push the canonical versions back to all IDE directories
3. **Commit**: Add `.agents/` to version control so the whole team benefits
4. **Existing files**: The original IDE-specific files are still in place — after verifying the sync works, you can optionally remove duplicates or let rosetta manage them going forward

## Important notes

- **Never delete existing files** without explicit user approval
- **Preserve content faithfully** — adapt format (frontmatter keys, extensions) but don't alter the actual instructions/content
- **Handle edge cases gracefully** — if a file has unusual structure or you're unsure how to translate it, tell the user and ask
- **Load `references/format-mapping.md`** if you need the exact translation rules between IDE formats

---
> Source: [tannishmango/rosetta](https://github.com/tannishmango/rosetta) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->

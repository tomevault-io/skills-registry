---
name: ez-context
description: Extract coding conventions and generate CLAUDE.md, AGENTS.md, Cursor rules, and more — with semantic drift detection. Use when this capability is needed.
metadata:
  author: vardhineediganesh877-ui
---

# ez-context: AI Context File Generator

Extract coding conventions and keep AI context files accurate.

## Steps

1. **Scan conventions** — Read the codebase to extract:
   ```bash
   # Build tool and language
   cat package.json | grep -E '"(name|version|main|scripts)"'
   cat tsconfig.json | grep -E '"(target|module|strict)"'
   ```
   - Language, framework, runtime version
   - Build commands, test commands, lint commands
   - Directory structure and module organization
   - Import/export conventions
   - Naming patterns (files, variables, functions)

2. **Extract architecture** — Map the codebase:
   ```bash
   ls src/
   grep -n "export " src/index.ts
   ```
   - Public API surface
   - Key interfaces and types
   - Module responsibilities
   - Dependency flow

3. **Detect drift** — Compare existing CLAUDE.md against actual code:
   ```bash
   # Check if documented modules exist
   grep -c "src/module.ts" CLAUDE.md
   test -f src/module.ts
   ```
   - Files mentioned that no longer exist
   - Commands that have changed
   - Versions that are outdated
   - Missing new modules or features

4. **Generate** — Create or update context files:
   - `CLAUDE.md`: Project conventions, build commands, architecture
   - `AGENTS.md`: Agent-specific instructions (if needed)
   - `.cursorrules`: Cursor IDE rules (if needed)
   - Keep existing structure, update only stale sections

5. **Verify** — Ensure generated files are accurate:
   - All mentioned files exist
   - All commands run successfully
   - No contradictions with actual code

## Rules
- Never remove information — only add or update
- Preserve the existing file structure and formatting
- Every command mentioned must actually work
- Every file path mentioned must actually exist
- Include version numbers and dates for temporal context

---
> Source: [vardhineediganesh877-ui/skillforge](https://github.com/vardhineediganesh877-ui/skillforge) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->

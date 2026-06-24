---
name: agent-context-wiring
description: Wire standards into CLAUDE.md, AGENTS.md, and .cursor/rules/ using references Use when this capability is needed.
metadata:
  author: sethdford
---

# Agent Context Wiring Skill

Ensure that standards are discoverable by agent files without duplicating content.

## Context

Agent files (CLAUDE.md, AGENTS.md, .cursor/rules) guide Claude and other developers through your standards. These files must reference standards — never duplicate them — so that updates to a standard are immediately visible to agents.

## Domain Context

The key principle of claude-standards is **reference, never duplicate**.

- Source of truth: `docs/standards/` contains standard documents
- Agent files: CLAUDE.md, AGENTS.md, .cursor/rules/ contain references to standards
- When a standard is updated, agents automatically see the new version
- Agent files are always kept in sync with reality

Reference: `levels/L1-context/templates/` for example context files

## Instructions

1. **Identify which agent tools are in use** — Determine which files guide your team
   - Does your project use CLAUDE.md? (Almost always yes)
   - Does your project use AGENTS.md? (Yes, if you have team processes)
   - Does your project use .cursor/rules/? (Optional, for IDE context)
   - Deliverable: Inventory of context files in your project

2. **Create or update CLAUDE.md** — Add standards references
   - Should reference the standards directory: `docs/standards/`
   - Should list applicable standards by category
   - Example entry: `See docs/standards/engineering/typescript.md for type safety rules`
   - Rule: Always use relative paths (`./docs/standards/...`)
   - Deliverable: Updated CLAUDE.md with standards references

3. **Create or update AGENTS.md** — Add team process guidance
   - Should explain which standards agents should focus on
   - Should reference ceremonies and enforcement processes
   - Example: `Before writing code, agents should read docs/standards/engineering/`
   - Rule: Always use relative paths
   - Deliverable: Updated AGENTS.md with standards references

4. **Create .cursor/rules/ files** (if applicable) — Add IDE-level rules
   - Can create separate rule files for different categories
   - Example: `.cursor/rules/engineering.md` references `docs/standards/engineering/`
   - Use consistent format across rule files
   - Deliverable: New or updated .cursor/rules/ files

5. **Verify standards are referenced, not duplicated** — Check for content duplication
   - Search for exact phrases from standards in agent files
   - If found, replace with reference: `See docs/standards/[category]/[standard].md`
   - Guard: Agent files should never contain more than 1-2 line summaries
   - Deliverable: Agent files contain only references, no duplicated standard content

6. **Run check-standards-refs.sh** — Verify all references are valid
   ```bash
   ./levels/L2-ceremonies/scripts/check-standards-refs.sh
   ```

   - This script finds broken references (standards that don't exist)
   - Fix any broken paths before merging
   - Deliverable: All verification scripts pass

## Anti-Patterns

1. **Duplicating standard content into context files** — Major maintenance burden
   - Wrong: Copy the entire error-handling standard into AGENTS.md
   - Right: "See docs/standards/engineering/error-handling.md for patterns"
   - Impact: When standard updates, agent file becomes stale
   - Guard: If you find yourself copy-pasting from a standard, stop and add a reference instead

2. **Missing wiring** — Standard exists but no agent sees it
   - Wrong: Write a new standard but forget to mention it in CLAUDE.md
   - Right: Create standard, add to README index, reference in CLAUDE.md
   - Impact: Standard is written but never discovered
   - Guard: Before marking a standard "done", verify it's referenced in at least one agent file

3. **Hardcoded file paths instead of relative paths** — Breaks in different contexts
   - Wrong: `/Users/someone/projects/repo/docs/standards/engineering/api.md`
   - Right: `./docs/standards/engineering/api.md`
   - Impact: Paths break when repo is cloned or moved
   - Guard: All paths should be relative to project root

4. **Inconsistent reference format** — Agent files don't follow a pattern
   - Wrong: Some files use `See docs/...`, some use `Read about...`, some use `Check...`
   - Right: Use consistent format: `See [Standard Title](./docs/standards/category/file.md)`
   - Guard: Use markdown link format for consistency: `[Title](path)`

## Further Reading

- `levels/L1-context/templates/` — Example CLAUDE.md and AGENTS.md
- `levels/L2-ceremonies/scripts/check-standards-refs.sh` — Reference verification script
- `AGENTS.md` (in this repo) — Example of well-wired agent context

---
> Source: [sethdford/claude-standards](https://github.com/sethdford/claude-standards) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->

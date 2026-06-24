---
name: cursor-rules
description: Project, user, and team rules that the Cursor SDK and Agent load — `.cursor/rules/*.mdc`, `AGENTS.md`, and the legacy `.cursorrules`. Use when authoring rules that the SDK will pick up via `local.settingSources`, or when writing rule frontmatter with the right type (always / auto-attached / agent-requested / manual). Use when this capability is needed.
metadata:
  author: HKTITAN
---

# cursor-rules

Rules are how you bake context into a project so every Agent run starts with the right instructions. The SDK reads them through `local.settingSources` — see [[../cursor-sdk/references/local-options]].

## When to use

- Adding repo-level coding conventions an SDK agent should follow
- Writing per-folder `AGENTS.md` for nested context
- Migrating from `.cursorrules` to `.cursor/rules/*.mdc`
- Choosing between always-on, auto-attached, agent-requested, and manual rules

## File locations

- [[references/file-locations]] — `.cursor/rules/`, `AGENTS.md`, legacy `.cursorrules`
- [[references/scope-and-precedence]] — team / project / user, hierarchy

## Authoring `.mdc` rules

- [[references/mdc-frontmatter]] — `description`, `globs`, `alwaysApply`
- [[references/rule-types]] — Always / Auto-attached / Agent-requested / Manual
- [[references/glob-patterns]] — `**/*.tsx`, comma-separated lists

## `AGENTS.md`

- [[references/agents-md]] — markdown-only, nested per directory
- [[references/agents-md-vs-rules]] — when to pick which

## SDK integration

- [[references/sdk-integration]] — `settingSources` and what loads where
- [[references/limitations]] — Tab and Inline Edit don't read rules

## Cross-links

- SDK setting sources → [[../cursor-sdk/references/local-options]]
- Hooks (sibling .cursor/ feature) → [[../cursor-hooks/SKILL]]
- Skills (sibling .cursor/ feature) → [[../cursor-skills-system/SKILL]]

---
> Source: [HKTITAN/cursor-sdk](https://github.com/HKTITAN/cursor-sdk) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->

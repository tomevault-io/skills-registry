---
name: setup
description: This skill should be used when the user asks to "set up cursor rules", "add clean code to cursor", "configure codex", "write cursorrules", "set up AGENTS.md", "add rules for codex", "set up all platforms", "configure all AI assistants", "sync rules to cursor", or when clean code rules need to be written for a specific AI coding assistant platform. Generates platform-native config files from .cleancode-rules.md. Use when this capability is needed.
metadata:
  author: DinoQuinten
---

# Clean Code Setup

Write platform-native config files from the project's `.cleancode-rules.md`. Each platform uses a different format — this skill handles the translation.

## Platforms

| Argument | File Written | Platform |
|---|---|---|
| `cursor` | `.cursorrules` | Cursor IDE |
| `codex` | `AGENTS.md` | OpenAI Codex CLI |
| `claude` | `CLAUDE.md` | Claude Code |
| `all` or empty | All of the above | All platforms |

## Before Writing

1. **Read `.cleancode-rules.md`** — check if it exists in the project root
   - If missing: tell the user to run `/cleancode:init` first to generate the ruleset
   - If present: read the active thresholds and language settings

2. **Check if files already exist** — if `.cursorrules` or `AGENTS.md` already exists, read it first to avoid overwriting custom content unrelated to clean code

3. **Detect primary language** from `tsconfig.json`, `package.json`, `*.py`, etc.

## Writing `.cursorrules` (Cursor)

Write to project root. If file already exists with other content, append a clearly marked `## Clean Code Rules` section rather than overwrite.

Read the template from `references/cursor-rules.md` and customize:
- Replace threshold values with those from `.cleancode-rules.md` if custom
- Enable or disable TypeScript-specific rules based on detected language
- Keep the file under 100 lines — Cursor reads the whole file

## Writing `AGENTS.md` (Codex CLI)

Write to project root. If file already exists, append a `## Clean Code Standards` section.

Read the template from `references/codex-agents.md` and customize:
- Replace thresholds with those from `.cleancode-rules.md` if custom
- Add project-specific examples if known
- Keep the formatting clean — Codex reads this as plain markdown

## Writing `CLAUDE.md` (Claude Code)

`/cleancode:init` handles `CLAUDE.md`. This skill focuses on the other platforms.

If the user runs `/cleancode:setup all`, also check if `CLAUDE.md` exists and append the clean code section if it's missing.

## Output Confirmation

After writing:

```
Platform config written:

  • .cursorrules → Cursor IDE rules (N lines)
  • AGENTS.md → OpenAI Codex CLI instructions (N lines)

All configs sourced from .cleancode-rules.md.

To update rules, edit .cleancode-rules.md then run:
  /cleancode:setup all
```

## Keeping Configs in Sync

When the user updates `.cleancode-rules.md` (changes thresholds, adds rules), they should re-run this skill to regenerate platform configs. The configs are generated output — `.cleancode-rules.md` is the source of truth.

## Additional Resources

### Reference Files

- **`references/cursor-rules.md`** — Full `.cursorrules` template with all clean code rules formatted for Cursor. Read this to get the exact content to write.
- **`references/codex-agents.md`** — Full `AGENTS.md` template formatted for OpenAI Codex CLI. Read this to get the exact content to write.

---
> Source: [DinoQuinten/claude-plugins](https://github.com/DinoQuinten/claude-plugins) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->

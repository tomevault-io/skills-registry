---
name: markdown-helper
description: Lint and fix Markdown files. Use this when the user asks to "check markdown", "fix formatting", or "lint docs". Use when this capability is needed.
metadata:
  author: spagestic
---

# Markdown Helper Skill

Uses `markdownlint-mcp` to ensure documentation quality.

## Usage

**Command Template:**

```bash
npx mcporter call --command "npx -y markdownlint-mcp" --tool <tool_name> --args <json_args>
```

## Common Tools

- `lint_markdown`: Check a file for style issues.

- `fix_markdown`: Automatically fix style issues.

Example: Lint a Markdown file

```bash
npx mcporter call --command "npx -y markdownlint-mcp" --tool fix_markdown --args '{"path": "docs/README.md"}'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spagestic) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

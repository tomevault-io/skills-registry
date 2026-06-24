---
name: vcp-context
description: > Use when this capability is needed.
metadata:
  author: z-m-huang
---

# VCP Context Injection

Load and inject VCP rule summaries into context so you write better code from the start.

## Step 1: Load and Validate Plugin Root

Read `.vcp/config.json` from the project root. Extract the `pluginRoot` field.

**If `.vcp/config.json` does not exist or `pluginRoot` is missing:** Stop and tell the user: "No VCP configuration found. Run `/vcp-init` to configure VCP for this project."

**Validate `pluginRoot`:** The path must be absolute, contain `/.claude/` (or `\.claude\` on Windows) as a path segment, and contain only safe path characters (letters, digits, `/`, `\`, `-`, `_`, `.`, `:`, and spaces). Reject any path with shell metacharacters (`;`, `&`, `|`, `$`, `` ` ``, `(`, `)`, `{`, `}`, `<`, `>`, `!`, `~`, `#`, `*`, `?`, `[`, `]`, `'`, `"`). If validation fails, stop and tell the user: "Invalid pluginRoot — must be within ~/.claude/ and contain no shell metacharacters. Run `/vcp-init` to fix." Also verify the file `<pluginRoot>/lib/vcp-context-core.ts` exists using Glob. If it does not exist, stop and tell the user: "pluginRoot points to an invalid VCP installation. Run `/vcp-init` to fix."

## Step 2: Generate Context

Run the context generation script:
```bash
bun "<pluginRoot>/lib/generate-context.ts" "<project-root>"
```

Replace `<pluginRoot>` with the value from Step 1 and `<project-root>` with the current project root path.

## Step 3: Output Result

Output the script's stdout directly. This is the formatted VCP Standards Context — scope-grouped rule summaries with token budget applied, ignores respected, and a cross-reference to `/vcp-audit`.

The TypeScript module handles all logic: config loading, manifest fetching, standard resolution, ignore filtering, rule extraction, scope grouping, severity ordering, and token budget truncation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/z-m-huang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

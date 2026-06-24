---
name: build-fix
description: Diagnose and fix build errors with minimal changes. Use when this capability is needed.
metadata:
  author: bis-code
---

# /build-fix

Spawns the `build-error-resolver` agent to diagnose build failures and apply targeted fixes.

## Steps

1. **Gather context** — determine the build command and current error state:
   - Check `.claude-toolkit.json` for `commands.build`
   - Fall back to auto-detection: `package.json` scripts, `Makefile` targets, language defaults
   - If arguments are provided (e.g., specific error output), pass those directly

2. **Spawn the agent** — use the Task tool:
   ```
   Task tool with subagent_type="build-error-resolver"
   ```
   Pass in the prompt:
   - The build command to run
   - Any captured error output (if available)
   - The project's language and framework context

3. **Present findings** — relay the agent's fix report:
   - Show initial error count vs final error count
   - List each fix applied with file path and description
   - Flag any remaining errors that need manual review

4. **Offer follow-up actions**:
   - "Run tests?" — verify fixes did not break test suite
   - "Review changes?" — spawn code-reviewer on the fixes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bis-code) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

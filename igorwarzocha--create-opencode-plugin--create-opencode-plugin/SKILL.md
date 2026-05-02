---
name: create-opencode-plugin
description: |- Use when this capability is needed.
metadata:
  author: igorwarzocha
---

# Creating OpenCode Plugins

<critical>
Re-read this file periodically during plugin development to refresh context and ensure you're following the correct procedure.
</critical>

<workflow>

## Workflow (Ordered)

1. **Verify SDK reference (HIGHLY RECOMMENDED)**
   - SHOULD regenerate references when possible. MAY skip if installed SDK is sufficient.
   - Run from this package root:
     ```bash
     bun run ./skills/create-opencode-plugin/scripts/extract-plugin-api.ts
     ```

2. **Validate feasibility**
   - MUST confirm the request is possible with available hooks.
   - If not feasible, suggest OC core changes, MCP tools, or scripts.

3. **Design**
   - READ: `references/hooks.md`, `references/hook-patterns.md`, `references/examples.md`, `references/CODING-TS.MD`.
   - Keep modules small; avoid monoliths.

4. **Implement**
   - READ: `references/tool-helper.md` (if tools), `references/events.md` (if events).
   - Follow the modular structure and common mistakes checklist.

5. **UI feedback (optional)**
   - READ: `references/toast-notifications.md`, `references/ui-feedback.md`.
   - Use `references/failure-guidance.md` if a feature fails.

6. **Test**
   - READ: `references/testing.md`.
   - Build first; use `dist/index.js` for local opencode.json.

7. **Publish (optional)**
   - READ: `references/publishing.md`, `references/update-notifications.md`.

---

## Feasibility

**Feasible**:
- Block/intercept tool calls
- React to events
- Add custom tools
- Modify LLM params
- Custom auth providers
- Customize session compaction
- Show status messages (toast/inline)

**Not feasible** (inform user and suggest alternatives):
- Modify TUI rendering/layout
- Add new built-in tools (requires OC source)
- Change core agent behavior/prompts
- Intercept assistant output mid-stream
- Add new keybinds or commands
- Modify internal file read/write
- Add new permission types

---

## Design Checklist

- Modular structure (types/utils/hooks/tools)
- Single-purpose files, <150 lines
- DRY and simple

**Plugin locations**:
- Local: `./index.ts`
- Project: `.opencode/plugin/<name>/index.ts`
- Global: `~/.config/opencode/plugin/<name>/index.ts`

**Context parameters**: `project`, `client`, `$`, `directory`, `worktree`

---

## Common Mistakes

- `client.registerTool()` -> use `tool: { name: tool({...}) }`
- Wrong event properties -> check `references/events.md`
- Sync hook handlers -> always `async`
- Missing block throw -> `throw new Error()` in `tool.execute.before`
- Missing types -> `import type { Plugin } from "@opencode-ai/plugin"`

---

## Debug Logging Policy

- Logging is ONLY allowed when the user reports a failure and the path to resolution is not obvious.
- If logging is needed, write to a single file next to `dist/index.js`.
- The log file MUST be overwritten on each run.
- Remove all logging before publishing.

---

## Quick Test

1. Build plugin.
2. Create `opencode.json` pointing to `dist/index.js`.
3. Run `opencode run hi`.

</workflow>

<reference_summary>

## Reference Files Summary

| File                      | Purpose                           | When to Read               |
| ------------------------- | --------------------------------- | -------------------------- |
| `hooks.md`                | Hook signatures (auto-generated)  | Workflow steps 3-4         |
| `events.md`               | Event types (auto-generated)      | Step 4 (if events)         |
| `tool-helper.md`          | Tool helper patterns              | Step 4 (if tools)          |
| `hook-patterns.md`        | Hook implementation examples      | Step 3-4                   |
| `CODING-TS.MD`            | Architecture principles           | Step 3                     |
| `examples.md`             | Complete plugin examples          | Step 3-4                   |
| `command-skip-llm.md`     | Command handled without LLM       | Step 4 (if commands)       |
| `config-bundling.md`      | Skills/agents/commands config     | Step 4 (if bundling)       |
| `toast-notifications.md`  | Toast popup API                   | Step 5 (if toasts)         |
| `ui-feedback.md`          | Inline message API                | Step 5 (if inline needed)  |
| `failure-guidance.md`     | User-facing failure guidance      | Step 5 (if failures occur) |
| `testing.md`              | Testing procedure                 | Step 6                     |
| `readme-template.md`      | End-user README template          | Step 7 (if publishing)     |
| `publishing.md`           | npm publishing                    | Step 7                     |
| `update-notifications.md` | Version toast pattern             | Step 7 (if npm plugins)    |

</reference_summary>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/igorwarzocha) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

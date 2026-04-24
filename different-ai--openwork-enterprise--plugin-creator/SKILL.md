---
name: plugin-creator
description: Create OpenCode plugins and know where to load them. Use when this capability is needed.
metadata:
  author: different-ai
---

## Quick Usage (Already Configured)

### Where plugins live
- Project plugins: `.opencode/plugins/*.js` or `.opencode/plugins/*.ts`
- Global plugins: `~/.config/opencode/plugins/*.js` or `.ts`

### Load from npm
Add npm plugin packages in `opencode.json`:
```json
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": ["opencode-helicone-session", "opencode-wakatime"]
}
```

## Minimal plugin template

```ts
export const MyPlugin = async ({ project, client, $, directory, worktree }) => {
  return {
    // Hook implementations go here
  }
}
```

## Notes from OpenCode docs

- Plugins are JS/TS modules exporting one or more plugin functions.
- Local plugins are loaded directly from the plugin directory.
- NPM plugins are installed via Bun at startup and cached in `~/.cache/opencode/node_modules/`.
- Load order: global config → project config → global plugins → project plugins.

## Reference

Follow the official OpenCode plugin docs: https://opencode.ai/docs/plugins/
Use the docs as the escape hatch when unsure.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/different-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

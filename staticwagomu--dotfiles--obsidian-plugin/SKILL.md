---
name: obsidian-plugin
description: Guide Obsidian plugin development following official guidelines. Use when creating/modifying Obsidian plugins, implementing commands, creating UI elements, or handling file operations. Use when this capability is needed.
metadata:
  author: staticwagomu
---

<purpose>
Develop Obsidian plugins following official best practices for security, API usage, and UI consistency.
</purpose>

<rules priority="critical">
  <rule>Use `this.app` - Never use global `app` object (debugging only)</rule>
  <rule>Build DOM safely - See `security.md` for mandatory patterns</rule>
  <rule>Use managed cleanup - `registerEvent()`, `addCommand()` for auto-cleanup</rule>
  <rule>NEVER detach leaves in `onunload()` - Obsidian handles this automatically</rule>
  <rule>No `innerHTML`, `outerHTML`, or `insertAdjacentHTML` usage</rule>
</rules>

<rules priority="standard">
  <rule>Minimize logging - Errors shown by default, not debug messages</rule>
  <rule>Replace placeholders - Change `MyPlugin`, `SampleSettingTab` to actual names</rule>
  <rule>All events registered via `registerEvent()`</rule>
  <rule>User file paths normalized with `normalizePath()`</rule>
  <rule>CSS uses Obsidian variables, not hardcoded colors</rule>
  <rule>Settings have general options at top (no heading)</rule>
</rules>

<patterns>
  <pattern name="project-structure">
```
my-plugin/
├── main.ts                 # Plugin entry point
├── settings.ts             # Settings tab and defaults
├── commands/               # Command handlers
├── views/                  # Custom views
├── modals/                 # Modal dialogs
└── styles.css              # Plugin styles (use CSS variables)
```
  </pattern>

  <pattern name="resource-management">
```typescript
// GOOD: Auto-cleanup on plugin unload
this.registerEvent(
    this.app.vault.on('create', (file) => { ... })
);

this.addCommand({
    id: 'my-command',
    name: 'My Command',
    callback: () => { ... }
});

// BAD: Manual cleanup required (avoid)
this.app.vault.on('create', this.handler);
```
  </pattern>
</patterns>

<constraints>
  <must>
    <rule>Apply this skill when creating new Obsidian plugins</rule>
    <rule>Apply when adding commands, settings, or views</rule>
    <rule>Apply when handling file/folder operations</rule>
    <rule>Apply when building plugin UI components</rule>
    <rule>Apply when reviewing Obsidian plugin code</rule>
  </must>
  <avoid>
    <rule>Using `innerHTML`, `outerHTML`, or `insertAdjacentHTML`</rule>
    <rule>Manual event listener cleanup instead of `registerEvent()`</rule>
    <rule>Hardcoded colors instead of CSS variables</rule>
    <rule>Using global `app` object in production code</rule>
  </avoid>
</constraints>

<best_practices>
  <practice priority="critical">See `security.md` for DOM manipulation rules</practice>
  <practice priority="high">See `api-patterns.md` for workspace, vault, and editor patterns</practice>
  <practice priority="high">See `ui-components.md` for settings, modals, and views</practice>
</best_practices>

<patterns>
  <pattern name="terminology">
| Use | Avoid |
|-----|-------|
| keyboard shortcut | hotkey |
| note | file (for .md) |
| folder | directory |
| select | click/tap |
| perform | invoke |

Use sentence case for headings. Bold button text in docs.
  </pattern>
</patterns>

<related_skills>
  <skill name="security.md">DOM manipulation rules (CRITICAL)</skill>
  <skill name="api-patterns.md">Workspace, vault, and editor patterns</skill>
  <skill name="ui-components.md">Settings, modals, and views</skill>
</related_skills>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/staticwagomu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

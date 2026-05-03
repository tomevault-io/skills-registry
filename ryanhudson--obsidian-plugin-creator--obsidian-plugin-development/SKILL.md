---
name: obsidian-plugin-development
description: This skill should be used when the user asks to "create an obsidian plugin", "build an obsidian plugin", "add a command to obsidian", "create a modal", "add a settings tab", "create a custom view", "add a sidebar view", "add a status bar item", "add an icon", "use setIcon", "support RTL languages", "test my plugin", "debug my plugin", "publish to obsidian community plugins", "create an editor extension", "add CodeMirror decorations", "add a context menu", "use the vault API", "optimize plugin performance", "use React in obsidian", "use Svelte in obsidian", "store API keys securely", "handle pop-out windows", "create a markdown processor", "manage workspace leaves", or mentions Obsidian plugin development, the Obsidian API, ItemView, WorkspaceLeaf, Lucide icons, right-to-left, CodeMirror 6, SecretStorage, or TypeScript plugins for Obsidian. Use when this capability is needed.
metadata:
  author: ryanhudson
---

# Obsidian Plugin Development

Comprehensive guidance for building Obsidian community plugins using TypeScript.

## Plugin Architecture

Obsidian plugins are TypeScript bundles that extend the app's functionality. Every plugin:

- Extends the `Plugin` class from `obsidian`
- Implements `onload()` for initialization
- Implements `onunload()` for cleanup
- Bundles to a single `main.js` file via esbuild

### Plugin Lifecycle

```ts
import { Plugin } from 'obsidian';

export default class MyPlugin extends Plugin {
  async onload() {
    // Configure resources, register commands, views, etc.
  }

  async onunload() {
    // Release resources (automatic for register* methods)
  }
}
```

### Project Structure

Organize code across multiple files:

```
src/
  main.ts           # Plugin entry point, lifecycle only
  settings.ts       # Settings interface and tab
  commands/         # Command implementations
  ui/               # Modals, views, components
  utils/            # Helpers and constants
  types.ts          # TypeScript interfaces
```

Keep `main.ts` minimal - delegate logic to modules.

## Core Capabilities

### Commands

Register user-invocable actions:

```ts
this.addCommand({
  id: 'my-command',        // Stable ID, never change after release
  name: 'My Command',      // User-visible name
  callback: () => { ... }, // Or editorCallback, checkCallback
});
```

Use `editorCallback` for editor access, `checkCallback` for conditional availability.

### Settings

Persist user configuration:

```ts
interface MySettings { enabled: boolean; }
const DEFAULT_SETTINGS: Partial<MySettings> = { enabled: true };

// In onload:
this.settings = Object.assign({}, DEFAULT_SETTINGS, await this.loadData());
this.addSettingTab(new MySettingTab(this.app, this));

// Save changes:
await this.saveData(this.settings);
```

### UI Components

- **Ribbon icons**: `this.addRibbonIcon('icon', 'tooltip', callback)`
- **Status bar**: `this.addStatusBarItem().setText('text')` (desktop only)
- **Modals**: Extend `Modal` or `SuggestModal`/`FuzzySuggestModal`
- **Views**: Extend `ItemView`, register with `registerView()`
- **Notices**: `new Notice('message')`

### Events and Cleanup

Always use `register*` methods for automatic cleanup:

```ts
this.registerEvent(this.app.vault.on('create', callback));
this.registerDomEvent(document, 'click', callback);
this.registerInterval(window.setInterval(callback, 1000));
```

## Development Workflow

### Setup

```bash
# Clone sample plugin or create from template
npm install
npm run dev    # Watch mode
npm run build  # Production build
```

### Testing

Copy build artifacts to vault:
```
<Vault>/.obsidian/plugins/<plugin-id>/
  main.js
  manifest.json
  styles.css (optional)
```

Reload Obsidian and enable in Settings → Community plugins.

### Debugging

- Open DevTools: Ctrl+Shift+I (Windows/Linux) or Cmd+Option+I (macOS)
- Use `console.log()` for debugging
- Check Console tab for errors

## Manifest (manifest.json)

Required fields:

```json
{
  "id": "plugin-id",
  "name": "Plugin Name",
  "version": "1.0.0",
  "minAppVersion": "1.0.0",
  "description": "What it does",
  "author": "Your Name",
  "isDesktopOnly": false
}
```

**Rules:**
- Never change `id` after release
- Use semantic versioning (x.y.z)
- Keep `minAppVersion` accurate
- Set `isDesktopOnly: true` only if using desktop-only APIs

## Best Practices

### Performance

- Keep startup light - defer heavy work
- Lazy-initialize expensive resources
- Batch disk access, debounce file events

### Security & Privacy

- Default to local/offline operation
- No hidden telemetry - require explicit opt-in
- Never execute remote code
- Disclose external services used
- Minimize vault access scope

### Code Quality

- TypeScript with `"strict": true`
- Bundle everything into main.js
- Use async/await, handle errors gracefully
- Keep main.ts minimal, split into modules

### Mobile Compatibility

- Test on iOS and Android if `isDesktopOnly: false`
- Avoid desktop-only APIs (Node, Electron)
- Mind memory constraints

## Common Patterns

### File Operations

```ts
// Read file
const content = await this.app.vault.read(file);

// Write file
await this.app.vault.modify(file, newContent);

// Create file
await this.app.vault.create(path, content);

// Get active file
const file = this.app.workspace.getActiveFile();
```

### Editor Operations

```ts
// In editorCallback:
const selection = editor.getSelection();
editor.replaceSelection(newText);
editor.getCursor(); // { line, ch }
editor.setLine(lineNum, text);
```

### Workspace

```ts
// Get active view
const view = this.app.workspace.getActiveViewOfType(MarkdownView);

// Open file
await this.app.workspace.openLinkText(path, '');

// Get leaves of type
const leaves = this.app.workspace.getLeavesOfType(VIEW_TYPE);
```

## Additional Resources

### Reference Files

For detailed API patterns and code examples, consult:

**Core Development:**
- **`references/agents-guide.md`** - Comprehensive AI-oriented development guide with coding conventions, file structure, common tasks, and do/don't guidelines
- **`references/ui-components.md`** - Detailed UI component examples (commands, views, modals, settings)
- **`references/publishing.md`** - Publishing workflow and community submission process

**Vault & Files:**
- **`references/vault-api.md`** - File operations, TFile/TFolder, read/cachedRead, process(), vault events

**Editor & Markdown:**
- **`references/editor-extensions.md`** - CodeMirror 6 state fields, view plugins, decorations, widgets
- **`references/markdown-processing.md`** - Post processors, code block processors for rendering content

**UI & Styling:**
- **`references/views.md`** - Complete views guide: ItemView, workspace, leaves, deferred views, state serialization
- **`references/icons.md`** - Lucide icons, setIcon(), addIcon() for custom SVGs, sizing
- **`references/status-bar.md`** - Status bar items, icons, dynamic updates (desktop only)
- **`references/context-menus.md`** - Menu class, file-menu/editor-menu events, submenus
- **`references/html-styling.md`** - createEl() helpers, CSS variables, dynamic styling
- **`references/rtl-support.md`** - Right-to-left language support, logical CSS properties, internationalization

**Frameworks:**
- **`references/frameworks.md`** - React integration overview, mounting in views
- **`references/svelte.md`** - Comprehensive Svelte 5 guide with runes, components, and patterns

**Advanced:**
- **`references/performance.md`** - onLayoutReady, startup optimization, deferred views, pitfalls
- **`references/multi-window.md`** - Pop-out window support, element.doc/win, instanceOf()
- **`references/secrets.md`** - SecretStorage API for secure API key/token storage

**Testing & Development:**
- **`references/testing-guide.md`** - Testing workflow, debugging, mobile testing, pre-release checklist

### Example Files

Working examples in `examples/`:

- **`examples/sample-main.ts`** - Complete main.ts with all common patterns
- **`examples/sample-settings.ts`** - Settings interface and tab implementation
- **`examples/sample-view.ts`** - Custom ItemView with state persistence, actions, and filtering
- **`examples/sample-editor-extension.ts`** - CodeMirror 6 state fields, view plugins, decorations
- **`examples/sample-markdown-processor.ts`** - Post processors, code block processors, widgets

### External Documentation

- [Obsidian API Docs](https://docs.obsidian.md)
- [Developer Policies](https://docs.obsidian.md/Developer+policies)
- [Plugin Guidelines](https://docs.obsidian.md/Plugins/Releasing/Plugin+guidelines)
- [Sample Plugin](https://github.com/obsidianmd/obsidian-sample-plugin)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ryanhudson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

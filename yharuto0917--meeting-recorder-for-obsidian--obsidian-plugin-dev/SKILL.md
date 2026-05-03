---
name: obsidian-plugin-dev
description: Comprehensive toolkit for building Obsidian plugins with TypeScript. Use when Claude needs to: (1) Create new Obsidian plugin projects with proper setup, (2) Work with Obsidian API (Vault, Workspace, MetadataCache, Plugin classes), (3) Build and deploy plugins to development or production environments, (4) Implement plugin features like commands, settings, views, or modals, (5) Follow Obsidian plugin best practices and guidelines. Includes project initialization scripts, API guides with practical examples, build/deploy workflows, and comprehensive reference documentation. Use when this capability is needed.
metadata:
  author: yharuto0917
---

# Obsidian Plugin Development

Comprehensive toolkit for creating, building, and deploying Obsidian plugins.

## Overview

This skill provides everything needed to develop Obsidian plugins, from initial project setup to production deployment. It includes automated scripts for project initialization, practical API usage guides, comprehensive reference documentation, and best practices for plugin development.

## Quick Start

### Creating a New Plugin

Use the initialization script to create a new plugin project:

```bash
./scripts/init_plugin.sh my-plugin ./my-plugin
cd my-plugin
```

This automatically creates:
- TypeScript configuration
- esbuild build setup
- Plugin manifest and metadata files
- Basic plugin structure with settings tab
- Package.json with necessary dependencies

### Setting Up Development Environment

Link your plugin to an Obsidian vault for live development:

```bash
./scripts/setup_dev_env.sh . ~/path/to/vault
npm run dev
```

Then in Obsidian:
1. Open Settings → Community plugins
2. Enable your plugin
3. Reload Obsidian (Ctrl/Cmd + R) when making changes

## Core Capabilities

### 1. Project Initialization

**Scripts:**
- `scripts/init_plugin.sh` - Create new plugin project with complete setup
- `scripts/setup_dev_env.sh` - Link plugin to Obsidian vault for development

**When to use:** Starting a new plugin project or setting up development environment.

### 2. API Usage

**Primary guide:** `references/api_guide.md` contains practical examples for:
- Plugin lifecycle (onload, onunload)
- File operations (Vault API)
- UI interactions (Workspace API)
- Metadata and caching (MetadataCache API)
- Commands and hotkeys
- Settings management
- Event handling

**When to use:** Implementing plugin features or working with Obsidian APIs.

**Quick example - Creating a file:**

```typescript
async createNote(filename: string, content: string): Promise<TFile> {
	const file = await this.app.vault.create(`${filename}.md`, content);
	return file;
}
```

**Quick example - Adding a command:**

```typescript
this.addCommand({
	id: 'my-command',
	name: 'My Command',
	callback: () => {
		console.log('Command executed');
	}
});
```

**For detailed API reference:** See `references/api_reference.md` for comprehensive documentation of all Obsidian API classes and methods.

### 3. Build and Deployment

**Guide:** `references/build_deploy.md` covers:
- Development workflow with hot reload
- Build configuration (esbuild, TypeScript)
- Testing strategies (manual and automated)
- Debugging techniques
- Publishing to Community Plugins
- Version management

**When to use:** Building for production, setting up CI/CD, or preparing for plugin release.

**Quick commands:**
```bash
npm run dev    # Development build with watch mode
npm run build  # Production build
```

### 4. Best Practices

**Guide:** `references/best_practices.md` includes:
- Code quality guidelines
- Performance optimization
- User experience principles
- Security and privacy
- Error handling patterns
- Documentation standards

**When to use:** Ensuring plugin quality, reviewing code, or learning proper patterns.

## Template Assets

The `assets/` directory contains template files for common plugin components:

- `custom-view-template.ts` - Template for custom views
- `modal-template.ts` - Template for modal dialogs
- `styles-template.css` - Template for plugin styles

**When to use:** Copy and modify these templates when implementing custom UI components.

## Common Workflows

### Creating a Simple Command Plugin

1. Initialize project with `init_plugin.sh`
2. Add command in main.ts:
   ```typescript
   this.addCommand({
     id: 'my-command',
     name: 'My Command',
     callback: () => {
       new Notice('Command executed!');
     }
   });
   ```
3. Build and test: `npm run dev`

### Working with Files

Refer to `references/api_guide.md` for examples:
- Creating files: Section "Working with Files (Vault)"
- Reading content: `app.vault.read(file)`
- Modifying files: `app.vault.modify(file, newContent)`
- Listing files: `app.vault.getMarkdownFiles()`

### Implementing Settings

1. Define settings interface
2. Load/save settings in plugin lifecycle
3. Create SettingTab class
4. See `references/api_guide.md` → "Settings" section for complete example

### Adding Custom Views

1. Copy `assets/custom-view-template.ts`
2. Customize the view class
3. Register view in plugin:
   ```typescript
   this.registerView(
     VIEW_TYPE_EXAMPLE,
     (leaf) => new ExampleView(leaf)
   );
   ```
4. See `references/api_guide.md` for more details

## Troubleshooting

### Plugin Not Loading
- Check console for errors (Cmd/Ctrl + Shift + I)
- Verify manifest.json is valid
- Ensure minAppVersion is compatible
- Try disabling other plugins

### Build Fails
- Run `npm install` to update dependencies
- Check for TypeScript errors
- Verify esbuild.config.mjs is correct

### Changes Not Reflecting
- Ensure dev build is running (`npm run dev`)
- Reload Obsidian (Ctrl/Cmd + R)
- Check if Hot Reload plugin is enabled

## Key Resources

- **API Guide:** `references/api_guide.md` - Practical examples for all common operations
- **API Reference:** `references/api_reference.md` - Complete API documentation
- **Build Guide:** `references/build_deploy.md` - Build, test, and deploy workflows
- **Best Practices:** `references/best_practices.md` - Code quality and patterns

## Plugin Structure Best Practices

Organize plugin code by concern:

```
src/
├── main.ts              # Plugin entry point
├── settings.ts          # Settings tab
├── commands/            # Command handlers
├── modals/              # Modal dialogs
├── views/               # Custom views
└── utils/               # Utility functions
```

## Publishing Checklist

Before submitting to Community Plugins:

- [ ] Code is clean and well-commented
- [ ] All features tested in multiple vaults
- [ ] No console errors
- [ ] README.md is complete
- [ ] manifest.json is accurate
- [ ] Production build created (`npm run build`)
- [ ] Plugin follows guidelines in `references/best_practices.md`

For complete publishing process, see `references/build_deploy.md` → "Publishing" section.

## Additional Notes

- Always use TypeScript strict mode for type safety
- Register event listeners with `registerEvent()` for automatic cleanup
- Provide user feedback with `Notice` for operations
- Handle errors gracefully with try/catch blocks
- Test on multiple platforms (Desktop and Mobile if applicable)
- Follow Obsidian's plugin guidelines for privacy and security

## Getting Help

- Obsidian Developer Documentation: https://docs.obsidian.md/
- Sample Plugin Repository: https://github.com/obsidianmd/obsidian-sample-plugin
- Obsidian API Types: https://github.com/obsidianmd/obsidian-api
- Community Forum: https://forum.obsidian.md/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yharuto0917) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

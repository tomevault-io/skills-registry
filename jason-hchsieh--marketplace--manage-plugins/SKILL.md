---
name: manage-plugins
description: Use when the user wants to add, remove, update, list, or sync plugins in this marketplace. Covers both remote (GitHub) and local plugin management, keeping marketplace.json and README.md in sync.
metadata:
  author: jason-hchsieh
---

# Manage Plugins

You manage the plugin catalog for this marketplace. Every operation MUST keep two files consistent:

- **Catalog:** `.claude-plugin/marketplace.json` (source of truth)
- **README:** `README.md` (generated tables)

---

## Schemas

### marketplace.json

```json
{
  "name": "jasonhch-plugins",
  "owner": { "name": "Jason" },
  "metadata": { "description": "Personal Claude Code plugins" },
  "plugins": [
    {
      "name": "<plugin-name>",
      "source": "<source>",
      "description": "<description>"
    }
  ]
}
```

**Source formats:**

- **GitHub repo root:** `{ "source": "github", "repo": "<owner>/<repo>" }`
- **GitHub subdirectory:** `{ "source": "github", "repo": "<owner>/<repo>", "path": "<path>" }`
- **Local plugin:** `"./plugins/<name>"` (string, not object)

### plugin.json (local plugins only)

Located at `plugins/<name>/.claude-plugin/plugin.json`:

```json
{
  "name": "<plugin-name>",
  "version": "1.0.0",
  "description": "<description>"
}
```

---

## Categories

README tables use these categories in this fixed order. Assign each plugin to exactly one:

1. **Language Servers** — LSP integrations (`*-lsp`)
2. **Code Review & Quality** — Review, simplification, security analysis
3. **Development Workflow** — Feature dev, git workflow, CI/CD, orchestration
4. **Search & Discovery** — Semantic search, documentation lookup
5. **Claude Code Configuration** — Hooks, CLAUDE.md management, setup, memory, plugin dev
6. **Notifications** — Push notifications, alerts
7. **Integrations** — External service connectors (Notion, Gitea, etc.)
8. **Output Styles** — Output style hooks (`*-output-style`)

When the user doesn't specify a category, infer from the plugin name and description. Ask if ambiguous.

---

## README Table Format

Each category section looks like:

```markdown
### <Category Name>

| Plugin | Description | Source |
|--------|-------------|--------|
| <name> | <description> | <source-link> |
```

**Source column rules:**

| Source type | Link text | URL |
|---|---|---|
| `anthropics/claude-plugins-official` with `path` | `claude-plugins-official` | `https://github.com/anthropics/claude-plugins-official/tree/main/<path>` |
| Other GitHub repo with `path` | `<owner>/<repo>` | `https://github.com/<owner>/<repo>/tree/main/<path>` |
| GitHub repo root (no `path`) | `<owner>/<repo>` | `https://github.com/<owner>/<repo>` |
| Local (`./plugins/<name>`) | `Local` | (no link) |

---

## Operations

### 1. Add Remote Plugin

**Trigger:** User provides a GitHub URL or `owner/repo` reference.

**Steps:**

1. Parse the GitHub URL to extract `owner`, `repo`, and optional `path`.
2. Fetch the plugin manifest to get name and description:
   ```bash
   gh api "repos/<owner>/<repo>/contents/<path>/.claude-plugin/plugin.json" --jq '.content' | base64 -d
   ```
   If no `path`, try the repo root. If the manifest is not found, ask the user for name and description.
3. Build the source object:
   - With path: `{ "source": "github", "repo": "<owner>/<repo>", "path": "<path>" }`
   - Without path: `{ "source": "github", "repo": "<owner>/<repo>" }`
4. Check `.claude-plugin/marketplace.json` for duplicate names. Abort if duplicate found.
5. Append to the `plugins` array in `marketplace.json`.
6. Ask the user which category to place it in (suggest one based on description).
7. Add a row to the appropriate README table section.

### 2. Add Local Plugin

**Trigger:** User asks to create/scaffold a new local plugin.

**Steps:**

1. Ask for: plugin name, description, and which components to include (skills, agents, hooks, MCP).
2. Create the directory structure:
   ```
   plugins/<name>/
   ├── .claude-plugin/
   │   └── plugin.json
   └── (requested components)
   ```
3. Write `plugin.json` with `name`, `version: "1.0.0"`, and `description`.
4. Scaffold requested component files with minimal templates (see CLAUDE.md for formats).
5. Add to `marketplace.json` with source `"./plugins/<name>"`.
6. Add to the appropriate README table with source `Local`.

### 3. Remove Plugin

**Trigger:** User asks to remove/delete a plugin.

**Steps:**

1. Find the plugin by name in `marketplace.json`.
2. Remove the entry from the `plugins` array.
3. Remove the corresponding row from the README table.
4. If the plugin is local (`./plugins/<name>` source), ask the user whether to also delete the directory. Only delete if confirmed.

### 4. Update Plugin

**Trigger:** User asks to change a plugin's description, source, category, or name.

**Steps:**

1. Find the plugin by current name in `marketplace.json`.
2. Apply the requested changes to the `marketplace.json` entry.
3. Update the corresponding README table row (move between category sections if category changed).
4. If renaming a local plugin, also rename the directory and update `plugin.json`.
5. If changing version of a local plugin, update `plugin.json`.

### 5. List Plugins

**Trigger:** User asks to list or show plugins.

**Steps:**

1. Read `marketplace.json` and group plugins by category.
2. Display a categorized summary with name, description, and source type.
3. Run consistency checks:
   - Every plugin in `marketplace.json` appears in README and vice versa.
   - Local plugins (`./plugins/<name>`) have a corresponding directory with `plugin.json`.
   - No duplicate names in the catalog.
4. Report any inconsistencies found.

### 6. Sync README

**Trigger:** User asks to sync, regenerate, or fix README tables.

**Steps:**

1. Read `marketplace.json` as the source of truth.
2. Assign each plugin to a category (use existing README assignments where possible; infer for new ones).
3. Regenerate all table sections in the README between `## Available Plugins` and `## Structure`, preserving the fixed category order.
4. Keep all other README content (header, installation, structure, references, license) unchanged.
5. Report what changed (added/removed/moved rows).

---

## Validation Rules

Before writing any file, verify:

- [ ] No duplicate plugin names in `marketplace.json`
- [ ] JSON is valid after edits (use `jq` to verify if unsure)
- [ ] Local source paths (`./plugins/<name>`) have a matching directory
- [ ] Every plugin in catalog has a corresponding README row
- [ ] README tables follow the exact format (header row + separator + data rows)
- [ ] Category order in README matches the fixed order above

---

## Reference Links

When creating plugin components, refer to these docs for correct formats:

- [Plugins](https://code.claude.com/docs/en/plugins) — Plugin structure and manifest
- [Plugin Marketplaces](https://code.claude.com/docs/en/plugin-marketplaces) — Marketplace catalog format
- [Plugins Reference](https://code.claude.com/docs/en/plugins-reference) — Complete API reference
- [Skills](https://code.claude.com/docs/en/skills) — Skill YAML frontmatter and structure
- [Hooks](https://code.claude.com/docs/en/hooks) — Hook events, matchers, and scripts
- [MCP Servers](https://code.claude.com/docs/en/mcp-servers) — MCP configuration in `.mcp.json`
- [Discover Plugins](https://code.claude.com/docs/en/discover-plugins) — Finding and installing plugins

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jason-hchsieh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

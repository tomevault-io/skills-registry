---
name: uninstall-plugin
description: Use when a user asks to fully remove a Neovim plugin, including plugin files, keymaps, config references, and README documentation.
metadata:
  author: tenfyzhong
---

# Uninstall Plugin Skill

Remove a Neovim plugin cleanly, including plugin files, related configuration, and docs.

## Input Format

```text
/uninstall-plugin <plugin-name|github-url|owner/repo>
```

Examples:

- `/uninstall-plugin flash.nvim`
- `/uninstall-plugin folke/flash.nvim`
- `/uninstall-plugin https://github.com/folke/flash.nvim`

## Workflow

### Step 1: Resolve Plugin Identity

Normalize input to these identifiers:

- `owner/repo` (if available)
- `repo` (for example `flash.nvim`)
- `plugin_name` (strip `.nvim` or `.vim`, for example `flash`)

### Step 2: Locate Plugin Spec File(s)

Find candidate files under `./lua/plugins/`:

```bash
# Direct repo match in specs
rg -n '"owner/repo"' lua/plugins

# Fallback by filename patterns
rg --files lua/plugins | rg 'plugin-name|repo|short-name'
```

If multiple candidates match, stop and ask the user which one to remove.

### Step 3: Scan Related References Before Deleting

Scan for references in code and docs:

```bash
rg -n 'owner/repo|repo-name|plugin_name|require\("module"\)' lua lsp README.md init.lua
```

Classify matches into:

- plugin spec files to delete
- keymaps to remove
- plugin-specific setup blocks to remove
- README entries to remove
- external tools notes to remove (only if no other plugin needs them)

### Step 4: Delete Plugin Files

Delete plugin-owned files only:

- `./lua/plugins/<plugin>.lua`
- plugin-specific support files (if clearly dedicated), for example:
  - `./lsp/<plugin-related>.lua`
  - `./lua/dev/<plugin-related>/`

Do not delete shared files unless only plugin-owned sections are removed.

### Step 5: Remove Plugin Config and Keymaps

Edit remaining files to remove plugin-specific blocks:

- keymaps in `./lua/keymap.lua` or other plugin specs
- plugin integration hooks in `./lua/*.lua`
- plugin-specific commands/autocmds/options

Rules:

1. Keep unrelated config untouched.
2. Preserve file style and formatting.
3. Prefer removing the smallest safe block.

### Step 6: Remove Documentation

Update `./README.md` and remove plugin-related content:

- key mapping rows introduced by the plugin
- plugin bullets in category sections
- command examples tied to the plugin
- required external tools only used by that plugin

If a section becomes empty, collapse or clean it.

### Step 7: Validate Cleanup

Run post-removal checks:

```bash
# Ensure no stale references remain
rg -n 'owner/repo|repo-name|plugin_name' lua lsp README.md init.lua

# Formatting and tests (required by this repository)
stylua --indent-type Spaces .
make test

# Markdown lint when docs changed
markdownlint-cli2 "**/*.md"
```

Do not manually edit `lazy-lock.json`; it is generated and should be updated by the plugin manager workflow.

## Output Format

After removal, report:

```text
✅ Removed plugin: <owner/repo or plugin>

Deleted files:
- <path>

Updated files:
- <path>: removed <what>

Verification:
- stylua: <pass/fail>
- make test: <pass/fail>
- markdownlint: <pass/fail or skipped>

Notes:
- <dependency or tool cleanup decisions>
```

## Safety Rules

1. Do not remove shared dependencies still referenced by other plugins.
2. If ownership is unclear, ask user before deleting.
3. Prefer precise edits over broad search/replace.
4. Keep changes reversible and reviewable.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tenfyzhong) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

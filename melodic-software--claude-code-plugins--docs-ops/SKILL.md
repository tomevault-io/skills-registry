---
name: docs-ops
description: Manage Cursor documentation lifecycle. Actions: scrape, validate, refresh, rebuild-index, clear-cache. Use when this capability is needed.
metadata:
  author: melodic-software
---

# Cursor Documentation Operations

Manage the Cursor documentation lifecycle through a single consolidated skill.

## Argument Routing

| Action | Description |
|--------|-------------|
| `scrape` | Scrape documentation from llms.txt sources, then refresh and validate |
| `validate` | Validate index integrity and detect drift (read-only) |
| `refresh` | Refresh index from filesystem without scraping |
| `rebuild-index` | Clear and immediately rebuild the search index |
| `clear-cache` | Clear the search cache (lazy rebuild on next search) |

Parse `$ARGUMENTS` to determine the action. The first token is the action keyword. Remaining tokens are passed as options to the action handler.

---

## Action: scrape

Scrape all documentation from the configured llms.txt sources.

### Steps

1. Run the scraping script:

```bash
python "${CLAUDE_PLUGIN_ROOT}/skills/cursor-docs/scripts/core/scrape_docs.py" --parallel
```

2. After scraping completes, rebuild the index:

```bash
python "${CLAUDE_PLUGIN_ROOT}/skills/cursor-docs/scripts/management/rebuild_index.py"
```

3. Verify the index:

```bash
python "${CLAUDE_PLUGIN_ROOT}/skills/cursor-docs/scripts/management/manage_index.py" verify
```

Report the results of each step.

---

## Action: validate

Run validation checks on the documentation index without making any modifications.

### Steps

1. Run index verification:

```bash
python "${CLAUDE_PLUGIN_ROOT}/skills/cursor-docs/scripts/management/manage_index.py" verify
```

2. Run full validation with summary:

```bash
python "${CLAUDE_PLUGIN_ROOT}/skills/cursor-docs/scripts/maintenance/validate_index.py"
```

3. Report document count:

```bash
python "${CLAUDE_PLUGIN_ROOT}/skills/cursor-docs/scripts/management/manage_index.py" count
```

Report the results of each validation step, highlighting any issues found.

---

## Action: refresh

Refresh the local index without re-scraping from the web. Use this when you've manually modified documentation files or need to rebuild the index.

### Steps

1. Rebuild the index from filesystem:

```bash
python "${CLAUDE_PLUGIN_ROOT}/skills/cursor-docs/scripts/management/rebuild_index.py"
```

2. Verify the index integrity:

```bash
python "${CLAUDE_PLUGIN_ROOT}/skills/cursor-docs/scripts/management/manage_index.py" verify
```

3. Report the document count:

```bash
python "${CLAUDE_PLUGIN_ROOT}/skills/cursor-docs/scripts/management/manage_index.py" count
```

Report the results of each step.

---

## Action: rebuild-index

Clear and immediately rebuild the cursor-docs search index. This is faster than `clear-cache` + waiting for next search because it triggers the rebuild immediately.

### When to Use

- After manually editing `index.yaml` or documentation files
- When search results seem stale or incorrect
- After a `git pull` with documentation changes
- When you need search working immediately

### Difference from clear-cache

| Action | Behavior | Search Availability |
|--------|----------|---------------------|
| `clear-cache` | Clears cache only | Rebuild on next search (lazy) |
| `rebuild-index` | Clears + rebuilds | Immediate (eager) |

### Options

- **No options**: Show plan and ask for confirmation
- **--force**: Skip confirmation and rebuild immediately

### Instructions

This action clears the Cursor documentation search cache and immediately rebuilds the index.

#### Check Current Status

First, check the current cache state. Report whether the cache exists, is valid, and when it was last built.

#### Request Confirmation

Unless the user passed `--force`, show a rebuild plan with the current cache state and ask for confirmation before proceeding. Explain that rebuilding takes a few seconds and search will be unavailable during the rebuild.

#### Clear and Rebuild

Once confirmed (or if `--force` was passed):

1. Clear the cache using the clear_cache.py script
2. Rebuild the index using rebuild_index.py
3. Verify the rebuild succeeded using manage_index.py verify

#### Report Results

Report the new index statistics including document count and build time. Confirm that search is now available.

```markdown
## Index Rebuilt

Successfully rebuilt Cursor documentation search index.

**New index stats:**
- Documents: X
- Build time: Xms

**Search is now available.**
```

### Error Handling

- **Plugin not installed:** Report "cursor-ecosystem plugin not found."
- **Rebuild failed:** Report error from script
- **Permission denied:** Report with remediation steps

---

## Action: clear-cache

Clear the cursor-docs search cache (inverted index). This forces the index to rebuild on the next documentation search.

### When to Use

- After manually editing `index.yaml` or documentation files
- When search results seem stale or incorrect
- After a `git pull` with documentation changes
- To free up disk space

### Options

- **No options**: Show what will be cleared and ask for confirmation
- **--force**: Skip confirmation and clear immediately

### Step 1: Parse Options

Check if `--force` flag is present.

```text
force_mode = "--force" in arguments (case-insensitive)
```

### Step 2: Locate Cache Directory

The cursor-docs cache is located at:

```text
${CLAUDE_PLUGIN_ROOT}/skills/cursor-docs/.cache/
```

Or via installed path:

```text
~/.claude/plugins/cache/<marketplace>/cursor-ecosystem/<version>/skills/cursor-docs/.cache/
```

### Step 3: Check Cache Status

List the cache files:

| File | Purpose |
|------|---------|
| `inverted_index.json` | Search index |
| `cache_version.json` | Hash-based validity tracking |

If cache directory doesn't exist or is empty, report: "Cache already clear. Nothing to do."

### Step 4: Confirmation (unless --force)

If NOT force_mode, present the cache clear plan:

```markdown
## Cache Clear Plan

**Target:** Cursor documentation search index

| File | Size |
|------|------|
| inverted_index.json | X.X MB |
| cache_version.json | 512 bytes |

**Total:** X.X MB

> **Note:** The search index will rebuild automatically on the next documentation search.
> For immediate rebuild, use the `rebuild-index` action after clearing.

**Proceed?** Reply "yes" to continue, or use `--force` to skip this confirmation.
```

### Step 5: Clear Cache

Use the clear_cache.py script to clear:

```bash
python "${CLAUDE_PLUGIN_ROOT}/skills/cursor-docs/scripts/maintenance/clear_cache.py"
```

### Step 6: Report Success

```markdown
## Cache Cleared

Successfully cleared Cursor documentation search cache.

**Cleared:**
- inverted_index.json
- cache_version.json

**Next steps:**
- Search index will rebuild automatically on next search
- Or use `rebuild-index` action to rebuild immediately
```

### Error Handling

- **Cache not found:** Report "Cache already clear or plugin not installed."
- **Permission denied:** Report "Permission denied. Try running with elevated privileges."
- **Plugin not installed:** Report "cursor-ecosystem plugin not found."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melodic-software) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

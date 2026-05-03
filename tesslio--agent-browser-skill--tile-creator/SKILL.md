---
name: tile-creator
description: Create tessl tiles containing docs, rules, or skills. Use when a user wants to create a tile, package content for tessl, or needs help with tile.json configuration. Also triggers on "create tile from", "convert to tile", "turn into tile", "make tile from", or "tile this repo" for existing content. Use when this capability is needed.
metadata:
  author: tesslio
---

# Tessl Tile Creator

Create tessl tiles from scratch or from existing content (repos, docs, packages).

## Understanding Content Types

| Type | Purpose | Agent Behavior |
|------|---------|----------------|
| **Docs** | Facts and knowledge | Agent *reaches* for info on-demand (like RAG) |
| **Skills** | Procedural workflows | Agent *does* new complex tasks |
| **Rules** | Critical constraints | Loaded into agent's instruction prompt |

**Most content should be docs or skills.** Rules are reserved for critical behavioral constraints only.

---

## Creating Tiles from Scratch

For greenfield tiles with no existing content.

### Workflow

1. **Determine content type**: docs, skills, or rules (usually just one)

2. **Create structure** (see [cli-commands.md](references/cli-commands.md) for full details):
   ```bash
   tessl tile new \
     --name workspace/tile-name \
     --summary "Description" \
     --workspace workspace \
     --path ./my-tile \
     --docs  # or --rules, --skill-name
   ```

3. **Add content**: Write markdown files in the appropriate folders

4. **Validate**:
   ```
   tessl tile lint ./my-tile
   ├── Pass → Done
   └── Fail → Fix errors → Re-run lint
   ```

### Tile Structure

```
my-tile/
├── tile.json
├── docs/           # Documentation (optional)
│   └── index.md
├── rules/          # Rules/steering (optional)
│   └── my-rule.md
└── skills/         # Skills (optional)
    └── my-skill/
        └── SKILL.md
```

---

## Creating Tiles from Existing Source

For converting existing repos, docs folders, or packages into tiles.

### Workflow

1. **Gather info**:
   - Source path (local folder, repo URL, or package name)
   - Output location for the tile
   - Desired workspace/tile name

2. **Analyze source**:
   - For **packages**: Read metadata (`package.json`, `pyproject.toml`, etc.), identify language, map public API
   - For **docs/repos**: Inventory existing markdown, assess structure, identify content types
   - Flag potential content: docs (facts), rules (behavioral - MUST/NEVER/always), skills (procedural workflows)

3. **Create tile structure**:
   ```bash
   tessl tile new \
     --name workspace/tile-name \
     --summary "Description" \
     --workspace workspace \
     --path ./output-tile \
     --docs
   ```
   For packages, add `--describes "pkg:ecosystem/name@version"`

4. **Transform content**:
   - Reorganize for **progressive disclosure**: index.md = overview + navigation, details in sub-pages
   - Apply **token efficiency**: concise examples over verbose explanations
   - Maintain **comprehensive coverage**: don't omit content, restructure instead
   - For packages: add `{ .api }` markers (see Package Documentation below)
   - If behavioral content detected: suggest to user before creating rules (keep minimal)
   - If procedural content detected: offer to convert to skills

5. **Configure tile.json**:
   - For packages: include `describes` field with purl
   - For non-packages: omit `describes`

6. **Validate**:
   ```
   tessl tile lint ./output-tile
   ├── Pass → Done
   └── Fail → Fix errors → Re-run lint
   ```

7. **Report**: Summarize what was created, note any gaps or assumptions for user review

### Index.md Structure

Flexible by source type:

**For packages:**
```markdown
# Package Name

Brief description.

## Installation
[install command]

## Quick Start
[basic usage example]

## API Reference
- [Module A](./module-a.md)
- [Module B](./module-b.md)
```

**For general docs:**
```markdown
# Title

Overview of what this documentation covers.

## Contents
- [Topic A](./topic-a.md) - Brief description
- [Topic B](./topic-b.md) - Brief description
```

---

## Package Documentation

When documenting software packages, use the `{ .api }` marker for API signatures.

### API Marker Format

Mark all public API elements:

```markdown
### createClient(options) { .api }

Creates a new client instance.

**Parameters:**
- `options.apiKey` (string) - API key for authentication
- `options.timeout` (number, optional) - Request timeout in ms

**Returns:** `Client` - The client instance
```

### tile.json for Packages

Include the `describes` field with a purl:

```json
{
  "name": "tessl/npm-example",
  "version": "2.0.0",
  "docs": "docs/index.md",
  "describes": "pkg:npm/example-sdk@2.0.0",
  "summary": "TypeScript SDK for Example API"
}
```

Purl format: `pkg:<ecosystem>/<name>@<version>`
- npm: `pkg:npm/openai@6.9.1`
- pypi: `pkg:pypi/requests@2.31.0` (normalize names: lowercase, hyphens)

---

## References

- **CLI commands**: See [cli-commands.md](references/cli-commands.md) for full `tessl tile new` usage
- **Docs format**: See [docs-format.md](references/docs-format.md)
- **Rules format**: See [rules-format.md](references/rules-format.md)
- **Skills format**: See [skills-format.md](references/skills-format.md)
- **tile.json spec**: See [tile-json.md](references/tile-json.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tesslio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
